# Dealing with money in software

## Problems

1. Decimals aren't always rounded

- Fixed sized representation like IEEE754 (the universal standard for floating-point arithmetic in computers) represents `0.1` as `0.1000000000000000055511151231257827021181583404541015625`. So if you sum `0.1 + 0.1 + 0.1`, you'll get something around `0.30000000000000004`. That means `0.00000000000000004` was generated from the air.

2. Decimals might not represent the reality

- If the software stores that you have `123.450012332`, where is the `0.000012332`? It doesn't represent real money, it's not connected to current currency reality

3. Fixed integers can easily have overflow issues (thin of inflation before telling to just set a very high size)

## Solutions

### Integer approach

Store all values as integers, given that the last two digits of the number represent the decimals. i.e. `$10.00` is represented as `10000`

Pros:

- Universal support accros programming languages and databases
- No rounding issues on additions and substractions

Cons:

- Can be problematic if dealing with multiple currencies, given some have less or more than two decimal values (Japanese Yen has 0 / Kuwaiti Dinar has 3)
- Multiplication and division will still require rounding
- Some tax calculations use more than 2 decimals for calculation
- If using not fixed Integers, the amount of storage and computation can grow fast

### Decimal approach

Store all values as decimals, with floating values. i.e. `$10.00` is represented as `10.0000`

Pros:

- Suitable for currencies with more or less decimal values

Cons:

- All layers of the application must be tightly aligned on how the decimals are represented, with how many decimal values are going to be used

## Other solutions

1. Store numbers as a rational number by storing two values:

- `a`
- `b`
- And the number really is `a/b`
