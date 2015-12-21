Calculate the Cost of Driving
=============================

Research several car-pooling websites. Create an application that calculates your daily driving cost, so that you can estimate how much money could be saved by car pooling, which also has other advantages such as reducing carbon emissions and reducing traffic congestion. The application should input the following information and display the user’s cost per day of driving to work:
- Total miles driven per day.
- Cost per gallon of gasoline.
- Average miles per gallon.
- Parking fees per day.
- Tolls per day.

``` c
//
//  main.c
//  CarCalulator
//
//  Created by Daniel Dyla on 10/29/15.
//  Copyright © 2015 Daniel Dyla. All rights reserved.
//

#include <stdio.h>

// declare all constants
const char* PROMPT_MILES = "How many miles do you drive per day?";
const char* PROMPT_CPG = "How much does gas cost? ($/gal)";
const char* PROMPT_MPG = "How efficient is the car? (mpg)";
const char* PROMPT_PARKING = "How much do you pay daily to park?";
const char* PROMPT_TOLLS = "How much do you pay daily in tolls?";
const char* OUTPUT = "This car costs $%.2f per day to drive\n";


int main(int argc, const char * argv[]) {
    // input variables
    float miles, cpg, mpg, parking, tolls;

    // calculation variables
    float total_cost;

    //get inputs
    puts(PROMPT_MILES);
    scanf("%f", &miles);

    puts(PROMPT_CPG);
    scanf("%f", &cpg);

    puts(PROMPT_MPG);
    scanf("%f", &mpg);

    puts(PROMPT_PARKING);
    scanf("%f", &parking);

    puts(PROMPT_TOLLS);
    scanf("%f", &tolls);

    //calculate cost
    total_cost = (cpg * (miles / mpg)) + parking + tolls;

    //output
    printf(OUTPUT, total_cost);

    return 0;
}
```
