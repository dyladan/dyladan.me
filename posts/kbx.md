Modeling beer price like a stock market
=======================================

Definitions
-----------

- Let `S(x)` be the [sigmoid function](https://en.wikipedia.org/wiki/Sigmoid_function).
- Let `t` be the time period between price changes
- Let `p` be the starting price for a given beer.
- Let `s` be the amount of $ you want to let the price "swing". E.G a 0.5 `s` would let a beer that costs $3 base cost from $2.5 to $3.5
- Let `c` be the amount you want to allow any given beer's x to change in a single trading period (I'm going to use .5).
- Let `l` be the largest absolute value of the difference between actual sales of each beer and the average sales per beer.
- Let `a` be the normalizing factor where `a = l/c`


Abusing the Sigmoid Function for fun and profit
-----------------------------------------------

We need to modify our sigmoid function so that we can control its min and max.

- Realize `S(x)` has a base price of $0.50 with a $0.50 swing.
- => `S(x)-.5` has a base price of $0 with a $0.50 swing.
- => `2(S(x)-.5)` has a base price of $0 with a $1 swing.
- => `2s * (S(x)-.5) + b` has a base price of `b` with a swing of `s`.

Description of the algorithm
----------------------------
Start with your list of beers `[b1,b2,b3,b4,b5]`.At the beginning of the night the price for all beers is its base price (`x=0`)
`[0,0,0,0,0]`.
At the end of each trading period, count the number of beers sold in the time period `[135, 152, 65, 103, 201]`.
Find the average number sold per beer (`131.2`) and find the difference between average and actual number sold for each beer
`[3.8, 20.8, -66.2, -28.2, 69.8]`. Normalize this against your `c` by dividing by `a` (139.6) to get `[0.03, 0.15, -0.47, -0.20, 0.5]`.
This becomes the change between the old x and the new x for each beer. If you never round, the average x will always be 0, however this
is not feasible so you may want to normalize x every few rounds to make sure your average x stays 0.
