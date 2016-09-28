# SQLite #
## Interacting ##
| Command           | Notes                                                       |
| --------          | -----                                                       |
| `.quit`           | Ends the session                                            |
| `.help`           | Provides help                                               |
| `.show`           | Show various settings                                       |
| `.mode line`      | Display fields vertically, similar to appending \G in MySQL |
| `.schema [table]` | Shows the creation query for the specified table(s)         |

## Day Calculations ##
```
julianday('2014-01-01')
```

Gives the Julian date.  Returns a floating point value representing days since
(???).  If no time is specified, assumes midnight which ends in .5.

Differences between days can be calculated with:
```
select julianday('2014-02-07') - julianday('2014-01-07');
select julianday(date('now')) - julianday('2014-01-01');
```

This will give a whole number (but floating point) representing how many 24-hour
periods between the two dates.
