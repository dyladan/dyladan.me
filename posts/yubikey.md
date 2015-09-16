The Yubikey Otp Algorithm
=========================

The yubikey is a fascinating device capable of generating passwords that
are only useful in the instant they are generated.
By the end of this post you should understand how passwords are generated,
encrypted, decrypted, parsed, and validated.

First steps
-----------

Before your yubikey can generate passwords, it must be set up to generate
valid keys. Specifically, this means it needs a public ID, a private ID,
and an AES128 key. The public ID is a 12 character modhex encoded string
(6 bytes).  Modhex is basically just hexadecimal, but instead of
0123456789abcdef, it uses cbdefghijklnrtuv. The creators of yubikey decided
to use this because it avoids keys that may not be found or work differently
on international keyboards. The private ID is a regular 12 character hexadecimal
string (6 bytes). The AES128 key is a 128 bit (16 byte) random number encoded
as a 32 character hexadecimal string. You can use the application provided
by yubikey to write these values to your key, and also to send them to
yubikey's hosted authentication service. This will be important later.

Generating the Password
-----------------------

Now that your key is capable of generating passwords and the yubico
auth service is capable of validating it, we can dive into how this algorithm
works. The first thing to do is use your key to generate an OTP like this one.

    vcbindnjbrglnjnkjrbeibifjunjrugctrltdilrrler

Note: I've created a yubikey just for the sake of this demo. I did not
send the info to yubico so it will not validate with their servers but you
are free to try :)

Decrypting the Password
-----------------------

The first thing the server does when it recieves your password is decrypt it
into a useable token using the AES key we sent it earlier. You may notice
that the first 12 characters of your generated token are always the same. You
may also notice that they are the 12 characters used as your public id. The
server uses this public ID to look up the AES key associated with your yubikey.
This key is then used to decrypt the password into a 32 character (16 byte)
hexadecimal token.

    >>> pubID, data = split_otp(vcbindnjbrglnjnkjrbeibifjunjrugctrltdilrrler)
    ('vcbindnjbrgl', 'njnkjrbeibifjunjrugctrltdilrrler')

    >>> aesKey = db_execute("SELECT pubID,aesKey FROM keys WHERE pubID=?;", 'vcbindnjbrgl')
    '8754cb76d94d74fb12987a9dc8c2b9fc'

    >>> decrypted_key = aes_decrypt(aesKey, data)
    'b7f5c2c4f8061000388837003206bf59'

At this point a simple CRC16 checksum is done on the decrypted token. The CRC16 must be
`0xf0b8` or the token is invalid. The reason for this is some magic based on
how CRC16 works and doing a checksum of a checksum. Someone more knowledgeable may be
able to explain why this is.

Parsing the Decrypted Token
---------------------------

The next thing the server must do is parse the decrypted token. This is a very simple
process and consists of splitting it up according to the following schema.

field|# chars|# bits|description
-----|-------|------|-----------
uid|12|192|The uid is the private ID for your yubikey
useCtr|4|64|The use counter is the number of sessions your yubikey has been used
tstp|6|96|The timestamp is randomly seeded when you plug in your yubikey, then increments at 8hz
sessionCtr|2|32|The number of times your yubikey has been triggered in the current session
rnd|4|64|A random value to increase entropy
crc|4|64|A crc16 checksum of the entire token with this field set as 0000

Validation
----------

Now that the validation server has decrypted and parsed the OTP it can validate
the password using a very simple process. After confirming that the uid is correct,
it simply makes sure, using the useCtr and sessionCtr, that this password hasn't
been used before. If the useCtr has incremented, then the server knows this
is a new password, if it the same, it checks the sessionCtr to make sure it
has incremented. If neither has incremented or the useCtr is lower than the
last used key, the server knows this is an old password and it should be rejected.
