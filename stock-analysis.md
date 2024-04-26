# Stock Analysis using MATCH_RECOGNIZE

Pattern Matching is key to anomaly detection and trend analysis. It is quite common to query the Data Warehouse to find patterns in data. One commonly overlooked SQL clause that allows detection of patterns in relational data is [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md).

[MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) clause is available in modern Warehouse Databases like #Snowflake and #Azure. Here is a sample query to identify stocks that show an increasing stock price trend for the most number of days. This is a simple example, but regex powered [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) can be used for sophisticated trend analysis.


```sql
select 
  symbol
  , days_of_increasing_stock_price
  , streak_start_date
  , streak_end_date
from us_stock_market_data_for_data_science.public.stock_history
match_recognize(
    partition by symbol
    order by date
    measures
        first(date) as streak_start_date,
        last(date) as streak_end_date,
        count(*) as days_of_increasing_stock_price
    one row per match
    pattern (increase+)
    define
          increase as close > lag(close)
)
order by days_of_increasing_stock_price desc;
```


| SYMBOL   | DAYS_OF_INCREASING_STOCK_PRICE | STREAK_START_DATE | STREAK_END_DATE |
|----------|--------------------------------|-------------------|-----------------|
| OPER     | 48                             | 2018-12-10        | 2019-08-07      |
| ETON     | 32                             | 2007-01-08        | 2007-02-28      |
| COE      | 31                             | 2011-08-25        | 2011-11-04      |
| ATRS.AS  | 25                             | 2006-11-15        | 2006-12-20      |
| MINT     | 24                             | 2019-01-08        | 2019-02-11      |
| MLMAI.PA | 23                             | 2007-10-08        | 2007-11-09      |
| FSV      | 22                             | 2019-01-14        | 2019-02-13      |
| ARAV     | 22                             | 2010-08-04        | 2010-09-14      |
| HMOP     | 22                             | 2020-05-01        | 2020-06-02      |
| RZB      | 21                             | 2016-08-01        | 2016-08-29      |
| FTAI     | 21                             | 2010-08-26        | 2010-09-27      |
| FSS      | 20                             | 1991-01-10        | 1991-02-06      |
| HLG      | 20                             | 2019-12-23        | 2020-01-22      |
| HMOP     | 20                             | 2020-07-14        | 2020-08-10      |
| IESC     | 20                             | 2003-03-27        | 2003-04-24      |
| RES      | 20                             | 1997-08-25        | 1997-09-22      |
| STAG     | 20                             | 2014-10-10        | 2014-11-06      |
| NP       | 20                             | 2013-06-27        | 2013-07-25      |
| DOL.TO   | 20                             | 2015-03-12        | 2015-04-09      |
| SXC      | 19                             | 2013-10-10        | 2013-11-05      |
| POR      | 19                             | 2020-01-06        | 2020-01-31      |
| CAT31.PA | 19                             | 2009-12-29        | 2010-01-25      |
| CIGI.TO  | 19                             | 2017-09-20        | 2017-10-17      |
| NSP      | 18                             | 2013-09-03        | 2013-09-26      |
| AFTY     | 18                             | 2017-12-28        | 2018-01-24      |
| NVEC     | 18                             | 2016-11-02        | 2016-11-28      |
| GWB      | 18                             | 2015-02-10        | 2015-03-06      |
| BLFS     | 18                             | 2021-10-12        | 2021-11-04      |
| DNR      | 18                             | 2005-05-25        | 2005-06-20      |
| NA.TO    | 18                             | 2016-02-26        | 2016-03-22      |
| CMO.PA   | 18                             | 2013-12-09        | 2014-01-06      |
| REXR     | 18                             | 2019-01-04        | 2019-01-30      |
| KBA      | 18                             | 2017-12-28        | 2018-01-24      |
| NOW      | 18                             | 2018-01-02        | 2018-01-26      |
| VCV      | 18                             | 2020-07-20        | 2020-08-11      |
| SUSL     | 18                             | 2021-10-13        | 2021-11-05      |
| SAFT     | 18                             | 2010-02-09        | 2010-03-05      |
| CRAP.PA  | 18                             | 2006-01-13        | 2006-02-07      |
| DORM     | 18                             | 2013-04-25        | 2013-05-20      |
| FTSL     | 18                             | 2016-02-26        | 2016-03-22      |
| PALL     | 18                             | 2019-12-23        | 2020-01-17      |
| WSFS     | 18                             | 2013-07-01        | 2013-07-25      |
| MCO      | 18                             | 2012-08-28        | 2012-09-21      |
| SRCL     | 18                             | 2018-12-31        | 2019-01-25      |
| ENB.TO   | 18                             | 2021-12-20        | 2022-01-18      |
| ETON     | 18                             | 2004-07-02        | 2004-07-29      |
| CRTO.PA  | 18                             | 2002-05-22        | 2002-06-14      |
| SJIJ     | 18                             | 2019-12-06        | 2020-01-02      |
| MGEE     | 18                             | 2018-06-12        | 2018-07-06      |
| SRLP     | 18                             | 2021-09-21        | 2021-10-14      |
| MUNI     | 18                             | 2010-08-03        | 2010-08-26      |
| PDI      | 17                             | 2018-05-15        | 2018-06-07      |
| BSMP     | 17                             | 2020-04-30        | 2020-05-27      |
| ERUS     | 17                             | 2019-12-04        | 2019-12-27      |
| CRLA.PA  | 17                             | 2010-03-11        | 2010-04-06      |
| CLM      | 17                             | 2012-01-20        | 2012-02-13      |
| EMNT     | 17                             | 2020-05-19        | 2020-06-11      |
| HLG      | 17                             | 2019-07-09        | 2019-07-31      |
| O        | 17                             | 2014-02-04        | 2014-02-27      |
| PGP      | 17                             | 2010-03-22        | 2010-04-14      |
| Y        | 17                             | 2006-11-13        | 2006-12-06      |
| CAF.PA   | 17                             | 2015-10-20        | 2015-11-11      |
| CHH      | 17                             | 2014-08-08        | 2014-09-02      |
| CETV     | 17                             | 2003-12-24        | 2004-01-20      |
| NNN      | 17                             | 2014-02-06        | 2014-03-03      |
| LSI      | 17                             | 2005-04-18        | 2005-05-10      |
| FTSL     | 17                             | 2019-02-04        | 2019-02-27      |
| IDRA     | 17                             | 2022-05-12        | 2022-06-06      |
| SCSC     | 17                             | 2010-03-01        | 2010-03-23      |
| ETON     | 17                             | 2003-10-30        | 2003-11-26      |
| IIIN     | 17                             | 2006-02-09        | 2006-03-06      |
| CSPI     | 17                             | 2022-05-13        | 2022-06-07      |
| EVRG     | 17                             | 2020-01-07        | 2020-01-30      |
| IWY      | 17                             | 2017-09-26        | 2017-10-18      |
| CHIX     | 17                             | 2017-12-28        | 2018-01-23      |
| ETON     | 17                             | 2008-02-21        | 2008-03-17      |
| CNF.PA   | 17                             | 2019-03-25        | 2019-04-16      |
| RUBY     | 17                             | 2013-07-03        | 2013-07-29      |
| JEF      | 17                             | 2021-10-01        | 2021-10-25      |
| KBCA.BR  | 17                             | 2016-05-12        | 2016-06-03      |
| NAII     | 17                             | 2011-09-27        | 2011-10-24      |
| FLRU     | 17                             | 2019-12-04        | 2019-12-27      |
| MPC      | 17                             | 2015-01-16        | 2015-02-10      |
| PSEC     | 17                             | 2021-10-01        | 2021-10-25      |
| UTF      | 17                             | 2014-08-07        | 2014-08-29      |
| PRTK     | 17                             | 2010-11-30        | 2010-12-22      |
| MSGN     | 17                             | 2014-05-15        | 2014-06-09      |
| TWIN     | 17                             | 2022-02-25        | 2022-03-21      |
| GSY.TO   | 17                             | 2015-10-28        | 2015-11-19      |
| XEI.TO   | 17                             | 2019-08-28        | 2019-09-20      |
| PDI      | 17                             | 2020-05-15        | 2020-06-09      |
| MDC      | 17                             | 2017-09-21        | 2017-10-13      |
| PFFA     | 17                             | 2019-12-18        | 2020-01-13      |
| BSCQ     | 17                             | 2020-05-14        | 2020-06-08      |
| HIE      | 17                             | 2019-08-29        | 2019-09-23      |
| CVA      | 17                             | 2014-10-17        | 2014-11-10      |
| ETM      | 17                             | 2003-11-20        | 2003-12-15      |
| MOD      | 17                             | 2022-07-19        | 2022-08-10      |
| EMB      | 17                             | 2012-08-20        | 2012-09-12      |
| LGL      | 17                             | 1998-02-17        | 1998-03-13      |

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
