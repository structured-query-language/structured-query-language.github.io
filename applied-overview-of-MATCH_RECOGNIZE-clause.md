# Applied overview of MATCH_RECOGNIZE clause

Pattern recognition is a common use case in data processing. Detecting trend reversals, identifying anomalies, and finding sequences in data are all examples of pattern recognition problems. In SQL, Row Pattern Recognition (RPR) became part of the SQL standard in 2016 and `MATCH_RECOGNIZE` was introduced. SQL `MATCH_RECOGNIZE` clause can perform Pattern Matching over a set of rows. `MATCH_RECOGNIZE` does this by assigning labels to events, finding the events within an ordered partition, and pulling out any sequences that match the given pattern

To demonstrate the power of `MATCH_RECOGNIZE` we will use the Unemployment by State data set from BLS.

## Analysis Goal
The goal of this analysis to take a look at the Unemployment data set and identify the States that are showing a decreasing Unemployment to date, and identify the State with the longest streak of decrease in Unemployment, month over month.

The Unemployment data look as following.
Sample of Unemployment data from BLS

Analysis Approach

We will use MATCH_RECOGNIZE to find the decreasing Unemployment Rate pattern PARTITIONed by State
Enter MATCH_RECOGNIZE

The `MATCH_RECOGNIZE` clause is used to search for a pattern in single field value spanning over multiple records. This clause enables you to define patterns using regular expressions and aggregate methods to verify and extract values from the match.

The following example shows the basic structure of a `MATCH_RECOGNIZE` clause:

```sql
select state, months_of_decreasing_unemployment, streak_start_date, streak_end_date
from unemployment_rate
match_recognize(
  partition by state
  order by yearmonth
  measures
    first(yearmonth) as streak_start_date,
    last(yearmonth) as streak_end_date,
    count(*) as months_of_decreasing_unemployment
  
  one row per match
  pattern (decrease+$)
  define
    increase as unemployment_rate > lag(unemployment_rate),
    decrease as unemployment_rate < lag(unemployment_rate)
)
order by months_of_decreasing_unemployment desc;
```


# MATCH_RECOGNIZE Query Syntax

```
SELECT ...
FROM ...
MATCH_RECOGNIZE (
  PARTITION BY ... -- Optional, used to partition the data
  ORDER BY ...      -- Defines the row sequence order
  MEASURES ...      -- Allows you to compute values based on the pattern
  ONE ROW PER MATCH -- Optional, outputs one row per match
  AFTER MATCH SKIP  -- Optional, skips rows after a match
    TO NEXT ...     -- Clause to define where to resume pattern matching
  PATTERN (...)     -- Defines the pattern to detect
  DEFINED AS ...    -- Defines conditions for the pattern elements
)
```

## PARTITION BY

`PARTITION BY` allows the match to be keyed and partitioned over a column name. A match will happen over every unique key specified by the partition statement. This enables a single query to be matched over all the keys and generate separate matches, one to every key.

## MEASURES

`MEASURES` is used to define the projected values from the match using aggregate methods. For example, LAST(YEARMONTH) AS STREAK_END_DATE will output the last YEARMONTH value that was found over all events that matched pattern named DECREASE into field name STREAK_END_DATE.

## PATTERN

The pattern defines the regular expression of events to be searched over the data stream. Pattern variables are user-defined and separated by spaces. Regex modifiers like ?, + and * can be used to modify the frequency of a variable when matching events.

    PATTERN (INIT+ (REVERSAL | MODIFICATION))

The pattern on this example defines a variable INIT at least once, followed by a concatenation of either REVERSAL or MODIFICATION.

Pattern quantifiers are used to change how a pattern is mapped in the data stream, defining how many times a pattern needs to match to be valid. The following quantifiers are available:

    '*' - Zero or more times
    '+' - One or more times
    '?' – Zero or one time
    '|' - One pattern or another
    '^' - Regex Start Anchor
    '$' - Regex End Anchor

    INIT REVERSAL+$

This example defines INIT 1 time, and REVERSAL 1 or more times, ending in REVERSAL (Regex End Anchor $).
DEFINE

DEFINE specifies the rules used to match a pattern variable to an event. The rules are Boolean expressions over aggregated values from the sequence of records.

```sql
DEFINE
  INCREASE AS UNEMPLOYMENT_RATE > LAG(UNEMPLOYMENT_RATE),
  DECREASE AS UNEMPLOYMENT_RATE < LAG(UNEMPLOYMENT_RATE)
```

This example defines rules INCREASE and DECREASE.

INCREASE defines the pattern where current UNEMPLOYMENT_RATE is greater than the previous UNEMPLOYMENT_RATE.

DECREASE defines the pattern where current UNEMPLOYMENT_RATE is less than the previous UNEMPLOYMENT_RATE.
Aggregate Methods

The following aggregate methods can be used in MEASURES and DEFINE:

    MIN – The minimum number aggregated so far.
    MAX – The maximum number aggregated so far.
    FIRST – The first value aggregated.
    LAST – The last value aggregated so far.

## Query Output

The above `MATCH_RECOGNIZE` query will produce the following results, which show the States with a Streak decreasing Unemployment Rate.
Rhode Island has a decreasing Unemployment Rate start July 2021.

We see that Rhode Island has a decreasing Unemployment Rate starting July 2021.



# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "MATCH_RECOGNIZE" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
