Salary Calculator
=================

Write a program to calculate the weekly salary of employees.

- Any time after 40 hours in a week should be counted as time and a half
- Loop until `-1` sentinel value

``` c
//
//  main.c
//  Salary Calculator
//
//  Created by Daniel Dyla on 11/10/15.
//  Copyright © 2015 Daniel Dyla. All rights reserved.
//

#include <stdio.h>
#define TRUE 1

float overtime_adjust(float hours);

int main(int argc, const char * argv[]) {
    float hours;
    float rate;
    float salary;

    while (TRUE) {
        puts("Enter # of hours worked (-1 to end):");
        scanf("%f", &hours);
        if (hours < 0) {
            break;
        }
        puts("Enter hourly rate of the worker ($00.00):");
        scanf("%f", &rate);
        hours = overtime_adjust(hours);

        salary = hours * rate;

        printf("Salary is $%.2f\n\n", salary);
    }


    return 0;
}

float overtime_adjust(float hours) {
    float overtime_hours;
    float regular_hours;
    float adjusted_hours;

    if (hours <= 40) {
        adjusted_hours = hours;
    }
    else {
        regular_hours = 40.0;
        overtime_hours = hours - regular_hours;
        adjusted_hours = regular_hours + overtime_hours * 1.5;
    }
    return adjusted_hours;
}
```
