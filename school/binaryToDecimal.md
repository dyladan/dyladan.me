Binary To Decimal Converter
===========================

Input an integer that consists of 0's and 1's and convert it into its decimal representation.

``` c
//
//  main.c
//  Binary to Decimal Converter
//
//  Created by Daniel Dyla on 11/10/15.
//  Copyright © 2015 Daniel Dyla. All rights reserved.
//

#include <stdio.h>

unsigned long long convert(unsigned long long num);
int numdigits(unsigned long long num);

int main(int argc, const char * argv[]) {
    unsigned long long input;
    unsigned long long output = 0;

    puts("Input a binary number:");
    scanf("%llu", &input);

    output = convert(input);

    printf("%llu\n", output);


    return 0;
}

unsigned long long convert(unsigned long long num) {
    int value = 1;
    unsigned long long output = 0;
    int digits = numdigits(num);

    for (int i = 0; i < digits; i++)
    {
        int digit = num % 10;
        num /= 10;
        output += digit * value;
        value *= 2;
    }
    return output;
}

int numdigits(unsigned long long number) {
    int digits = 0;

    if (number == 0) return 1;

    while (number) {
        number /= 10;
        digits++;
    }

    return digits;
}
```
