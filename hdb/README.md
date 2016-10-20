# Build hdb
The script `buildhdb.q` builds a small historical database, useful when learning q. The main tables are partitioned on date, and optionally segmented on a round robin basis.

Prices and sizes are generated by random walks, selecting those that do not wander too far from outset. Prices are correlated.

There are 15 stocks. If you change this, also change corresponding definitions of starting prices, etc.

Take care to ensure that the target folders are empty and writable. With the default configuration, you may need to create the start subdirectory.

## Default hdb
The first few lines are the configuration, modify as needed:
~~~~
dst:`:start/db      / database root (relative to q directory)
dsp:""              / optional database segments root
dsx:5               / number of segments
bgn:2013.05.01      / begin, end
end:2013.05.31      / (only mon-fri used)
nt:1000             / trades per stock per day
qpt:5               / quotes per trade
npt:3               / nbbo per trade
nl2:1000            / level II entries in day
\S 104831           / random seed
~~~~
With the default config, the database generated will be about 100MB, with about 2 million trades and quotes (these values depend on the random seed).

## Segmented hdb
The database can be segmented. Usually this would be done over different drives, but for illustration, the segments are here written to the same drive. The database segment directory needs to be defined. For the segmented database, try changing the data to 4 year's worth, for example:
~~~~
dst:`:start/dbs     / new database root
dsp:`:/dbss         / database segments root
bgn:2010.01.01      / begin, end
end:2013.12.31      / 4 years
~~~~
This writes the daily data to folders `/dbss/d0`, `/dbss/d1`, etc. and creates `par.txt` in the database root.

With this config, the database generated will be about 4GB, with about 75 million trades and quotes (these values depend on the random seed).

## Tables
The tables are:

- daily - daily open/high/low/close
- depth - last day depth of book
- mas - stock name master
- nbbo - best bid/offer
- quote - quotes
- trade - trades

For example:
~~~~
q)\l start/db
q)count quote
1846241
q)3#select from quote where date=last date, sym=`IBM
date       sym time         bid   ask   bsize asize mode ex
-----------------------------------------------------------
2013.05.31 IBM 09:30:00.004 47.12 48.01 21    72    O    N
2013.05.31 IBM 09:30:00.008 46.98 48.14 93    17    Z    N
2013.05.31 IBM 09:30:00.013 47.22 47.99 55    19    A    N
~~~~
cdb
17 Oct 2013