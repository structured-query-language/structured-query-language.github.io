# DECODE

`IFF` is a conditional expression Snowflake function that evaluates to a Single-level `if-then-else` expression. However `IFF` only allows a single condition. What if you need to implement a `if-then-elseif-else-endif`? Most people will opt to use `CASE STATEMENT`. While `CASE STATEMENT` will do the trick, it is not most optimal solution for a `if-then-elseif-else-endif` or even a `if-then-elseif-else-if-else-endif` conditional expression. `DECODE` function is a versatile Snowflake Conditional Expression Function that lets you implement `if-then-elseif-else-if-else-endif` expression without the use of a `CASE STATEMENT`. Here is an example:



