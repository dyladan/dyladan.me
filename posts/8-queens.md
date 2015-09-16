# My solutions to the classic 8 queens problem

I recently came across a well known problem in computer science called the Eight Queens problem.
For those unfamiliar with the problem, it stipulates that the user be given a regular 8x8 chess
board and 8 queens. The user is then asked to place the queens on the board such that no queen
may attack or be attacked by another queen in a single move.

## rules:

- No 2 queens may be in the same row
- No 2 queens may be in the same column
- No 2 queens may be on the same diagonal line.

Now, at first appearance, this may not seem all that difficult. If you're one of those people,
I invite you to go get a chessboard and 8 pieces and give it a try without the help of a computer.
For the rest of us, this is an excellent chance to break into a programming language called haskell.

My first idea was to generate every possible board and filter my results.
I quickly whipped up some code to create a list of 2d arrays by enumerating all possible arrays
with 8 queens and started it running.
My computer ran out of RAM in seconds.
Needless to say, this wasn't the best idea.
I quickly realized that I needed a more compact representation of the boards in memory and soon
decided to represent the board as a 8-tuple like this: `(a,b,c,d,e,f,g,h)`.
This had the advantage of not only cutting memory usage to 1/8 per board of what it had been,
but also if each column is a field, we have now immediately excluded any boards where two queens
are in the same column. If you have a lot of RAM, you can calculate every such board like this:

```
boards = [(a,b,c,d,e,f,g,h) |
  a <- [1..8],
  b <- [x | x <- [1..8]],
  c <- [x | x <- [1..8]],
  d <- [x | x <- [1..8]],
  e <- [x | x <- [1..8]],
  f <- [x | x <- [1..8]],
  g <- [x | x <- [1..8]],
  h <- [x | x <- [1..8]]]
```

If you're a mathmatician (and probably if you're not), you probably realize that this would
generate an insane number of boards (`8^8` or `16,777,216` to be exact). From here, you could
feasibly filter out every board and return the valid ones with haskell's `filter` function.
There is another way, however, to drastically reduce the number of boards. We can
use conditional statements in our list comprehensions to ensure that we only return boards
where no two queens are on the same row right off the bat.

```
boards = [(a,b,c,d,e,f,g,h) |
  a <- [1..8],
  b <- [x | x <- [1..8], x /= a],
  c <- [x | x <- [1..8], x /= a, x /= b],
  d <- [x | x <- [1..8], x /= a, x /= b, x /= c],
  e <- [x | x <- [1..8], x /= a, x /= b, x /= c, x /= d],
  f <- [x | x <- [1..8], x /= a, x /= b, x /= c, x /= d, x /= e],
  g <- [x | x <- [1..8], x /= a, x /= b, x /= c, x /= d, x /= e, x /= f],
  h <- [x | x <- [1..8], x /= a, x /= b, x /= c, x /= d, x /= e, x /= f, x /= g]]
```

This generates a significantly reduced `8!` results (`40320`). This is an improvement of over 400
times! I prefer to do it this way because it eliminates large amounts of boards very early in the
calculation. For instance, the first board it generates begins with `(1,1...`. After generating the
second `1` not only does it stop computing the board because it already knows the board to be invalid,
but it skips generating any board which starts with `(1,1...` and immediately moves on to `(1,2...`
(which is a valid start).

From here it is certainly feasible to determine which boards are valid with a filter, but I don't think
I'd do it that way. In my list comprehension I am already making fairly heavy use of predicates to
filter out most of the invalid boards. Why not use just a few more to filter out the boards where
queens are diagonal from each other? Such a list comprehension would look like this:

```
boards = [(a,b,c,d,e,f,g,h) |
  a <- [1..8],
  b <- [x | x <- [1..8], x /= a, x /= a+1, x /= a-1],
  c <- [x | x <- [1..8], x /= a, x /= a+2, x /= a-2, x /= b, x /= b+1, x /= b-1],
  d <- [x | x <- [1..8], x /= a, x /= a+3, x /= a-3, x /= b, x /= b+2, x /= b-2, x /= c, x /= c+1, x /= c-1],
  e <- [x | x <- [1..8], x /= a, x /= a+4, x /= a-4, x /= b, x /= b+3, x /= b-3, x /= c, x /= c+2, x /= c-2, x /= d, x /= d+1, x /= d-1],
  f <- [x | x <- [1..8], x /= a, x /= a+5, x /= a-5, x /= b, x /= b+4, x /= b-4, x /= c, x /= c+3, x /= c-3, x /= d, x /= d+2, x /= d-2, x /= e, x /= e+1, x /= e-1],
  g <- [x | x <- [1..8], x /= a, x /= a+6, x /= a-6, x /= b, x /= b+5, x /= b-5, x /= c, x /= c+4, x /= c-4, x /= d, x /= d+3, x /= d-3, x /= e, x /= e+2, x /= e-2, x /= f, x /= f+1, x /= f-1],
  h <- [x | x <- [1..8], x /= a, x /= a+7, x /= a-7, x /= b, x /= b+6, x /= b-6, x /= c, x /= c+5, x /= c-5, x /= d, x /= d+4, x /= d-4, x /= e, x /= e+3, x /= e-3, x /= f, x /= f+2, x /= f-2, x /= g, x /= g+1, x /= g-1]]
```

It's a little long I think we can all agree, but it computes the 8 queens problem in
far less time than I could with a pen and paper :) Maybe in the future I'll see about
making it a little more efficient or generalize it for any size board.
