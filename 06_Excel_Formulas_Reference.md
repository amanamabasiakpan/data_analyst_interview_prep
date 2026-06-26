# Excel Formulas, Functions & Quick Reference

## Table of Contents
1. [Essential Functions by Category](#essential-functions-by-category)
2. [Financial Functions](#financial-functions)
3. [Date & Time Functions](#date--time-functions)
4. [Text Functions](#text-functions)
5. [Lookup & Reference Functions](#lookup--reference-functions)
6. [Statistical Functions](#statistical-functions)
7. [Logical Functions](#logical-functions)
8. [Math & Trig Functions](#math--trig-functions)
9. [Information Functions](#information-functions)
10. [Dynamic Array Functions (Excel 365)](#dynamic-array-functions-excel-365)
11. [Power Query M Language Quick Ref](#power-query-m-language-quick-ref)
12. [DAX Quick Reference](#dax-quick-reference)
13. [Keyboard Shortcuts](#keyboard-shortcuts)

---

## Essential Functions by Category

### Must-Know Functions (Top 25)

| # | Function | Purpose | Example |
|---|----------|---------|---------|
| 1 | SUM | Add numbers | `=SUM(A1:A10)` |
| 2 | AVERAGE | Mean of numbers | `=AVERAGE(A1:A10)` |
| 3 | COUNT | Count numbers | `=COUNT(A1:A10)` |
| 4 | COUNTA | Count non-empty | `=COUNTA(A1:A10)` |
| 5 | MAX | Largest value | `=MAX(A1:A10)` |
| 6 | MIN | Smallest value | `=MIN(A1:A10)` |
| 7 | IF | Conditional logic | `=IF(A1>100, "High", "Low")` |
| 8 | SUMIF | Sum with condition | `=SUMIF(A1:A10, ">50")` |
| 9 | COUNTIF | Count with condition | `=COUNTIF(A1:A10, "Yes")` |
| 10 | VLOOKUP | Vertical lookup | `=VLOOKUP(A1, B1:D10, 3, FALSE)` |
| 11 | INDEX | Array element | `=INDEX(A1:C10, 2, 3)` |
| 12 | MATCH | Position in range | `=MATCH(A1, B1:B10, 0)` |
| 13 | IFERROR | Error handling | `=IFERROR(A1/B1, 0)` |
| 14 | CONCATENATE/TEXTJOIN | Join text | `=TEXTJOIN(", ", TRUE, A1:A5)` |
| 15 | LEFT/RIGHT/MID | Extract text | `=LEFT(A1, 3)` |
| 16 | TRIM | Remove extra spaces | `=TRIM(A1)` |
| 17 | ROUND | Round number | `=ROUND(A1, 2)` |
| 18 | SUMIFS | Sum with multiple conditions | `=SUMIFS(C:C, A:A, "Sales", B:B, ">1000")` |
| 19 | COUNTIFS | Count with multiple conditions | `=COUNTIFS(A:A, "Active", B:B, ">2024-01-01")` |
| 20 | AVERAGEIFS | Average with conditions | `=AVERAGEIFS(C:C, A:A, "Engineering")` |
| 21 | DATE | Create date | `=DATE(2024, 6, 15)` |
| 22 | TODAY | Current date | `=TODAY()` |
| 23 | NOW | Current date/time | `=NOW()` |
| 24 | EOMONTH | End of month | `=EOMONTH(TODAY(), 0)` |
| 25 | PMT | Loan payment | `=PMT(rate/12, years*12, -loan_amount)` |

---

## Financial Functions

### Loan & Investment
```excel
=PMT(rate, nper, pv, [fv], [type])           -- Payment
=IPMT(rate, per, nper, pv, [fv], [type])      -- Interest portion
=PPMT(rate, per, nper, pv, [fv], [type])      -- Principal portion
=CUMIPMT(rate, nper, pv, start_period, end_period, type)  -- Cumulative interest
=CUMPRINC(rate, nper, pv, start_period, end_period, type) -- Cumulative principal

=FV(rate, nper, pmt, [pv], [type])           -- Future value
=PV(rate, nper, pmt, [fv], [type])            -- Present value
=NPER(rate, pmt, pv, [fv], [type])            -- Number of periods
=RATE(nper, pmt, pv, [fv], [type])            -- Interest rate
```

### Investment Analysis
```excel
=NPV(rate, value1, value2, ...)               -- Net present value
=IRR(values, [guess])                         -- Internal rate of return
=MIRR(values, finance_rate, reinvest_rate)    -- Modified IRR
=XNPV(rate, dates, values)                    -- NPV with irregular dates
=XIRR(values, dates, [guess])                  -- IRR with irregular dates
```

### Depreciation
```excel
=SLN(cost, salvage, life)                   -- Straight-line
=DB(cost, salvage, life, period, [month])     -- Declining balance
=DDB(cost, salvage, life, period, [factor])  -- Double declining
=SYD(cost, salvage, life, per)                -- Sum of years digits
=VDB(cost, salvage, life, start_period, end_period) -- Variable declining
```

### Securities
```excel
=ACCRINT(issue, first_interest, settlement, rate, par, frequency)  -- Accrued interest
=PRICE(settlement, maturity, rate, yld, redemption, frequency)    -- Bond price
=YIELD(settlement, maturity, rate, pr, redemption, frequency)     -- Bond yield
=COUPNUM(settlement, maturity, frequency)                         -- Coupons remaining
```

---

## Date & Time Functions

### Current Date/Time
```excel
=TODAY()                    -- Current date (no time)
=NOW()                     -- Current date and time
```

### Date Construction & Extraction
```excel
=DATE(year, month, day)                    -- Create date
=DATEVALUE(date_text)                      -- Convert text to date
=TIME(hour, minute, second)                -- Create time
=TIMEVALUE(time_text)                      -- Convert text to time

=YEAR(date)                                -- Extract year
=MONTH(date)                               -- Extract month
=DAY(date)                                 -- Extract day
=WEEKDAY(date, [return_type])              -- Day of week (1=Sun, 2=Mon...)
=WEEKNUM(date, [return_type])              -- Week number

=HOUR(time)                                -- Extract hour
=MINUTE(time)                              -- Extract minute
=SECOND(time)                              -- Extract second
```

### Date Arithmetic
```excel
=EDATE(start_date, months)                 -- Date N months away
=EOMONTH(start_date, months)               -- End of month N months away
=WORKDAY(start_date, days, [holidays])     -- Working day N days away
=WORKDAY.INTL(start_date, days, [weekend], [holidays])  -- Custom weekend
=NETWORKDAYS(start_date, end_date, [holidays])           -- Working days between
=NETWORKDAYS.INTL(start_date, end_date, [weekend], [holidays])  -- Custom weekend

=DATEDIF(start_date, end_date, "Y")        -- Years between
=DATEDIF(start_date, end_date, "M")        -- Months between
=DATEDIF(start_date, end_date, "D")        -- Days between
=DATEDIF(start_date, end_date, "YM")       -- Months excluding years
=DATEDIF(start_date, end_date, "YD")       -- Days excluding years
```

### Date Formatting in Formulas
```excel
=TEXT(date, "dddd, mmmm dd, yyyy")         -- "Monday, June 15, 2024"
=TEXT(date, "mmm-yy")                      -- "Jun-24"
=TEXT(date, "yyyy-mm-dd")                  -- "2024-06-15"
=TEXT(date, "mmmm")                        -- "June"
```

---

## Text Functions

### Case Conversion
```excel
=UPPER(text)                               -- "HELLO"
=LOWER(text)                               -- "hello"
=PROPER(text)                              -- "Hello World"
```

### Extraction
```excel
=LEFT(text, [num_chars])                   -- First N characters
=RIGHT(text, [num_chars])                  -- Last N characters
=MID(text, start_num, num_chars)           -- Middle characters
=LEN(text)                                 -- Number of characters
=FIND(find_text, within_text, [start_num]) -- Position (case-sensitive)
=SEARCH(find_text, within_text, [start_num]) -- Position (not case-sensitive)
```

### Manipulation
```excel
=TRIM(text)                                -- Remove extra spaces
=CLEAN(text)                               -- Remove non-printable characters
=SUBSTITUTE(text, old_text, new_text, [instance_num])  -- Replace text
=REPLACE(old_text, start_num, num_chars, new_text)       -- Replace by position
=CONCAT(text1, text2, ...)                 -- Join text
=TEXTJOIN(delimiter, ignore_empty, text1, text2, ...)  -- Join with delimiter
=REPT(text, number_times)                  -- Repeat text
```

### Text to Number / Number to Text
```excel
=VALUE(text)                               -- Text to number
=TEXT(number, format_text)                 -- Number to text
=NUMBERVALUE(text, [decimal_separator], [group_separator])  -- Locale-aware conversion
=FIXED(number, [decimals], [no_commas])     -- Number to text with commas
```

### Pattern Matching
```excel
=EXACT(text1, text2)                       -- Case-sensitive comparison
=ISNUMBER(SEARCH("pattern", text))         -- Check if contains
=IF(ISNUMBER(SEARCH("@", A1)), "Email", "Not Email")
```

---

## Lookup & Reference Functions

### VLOOKUP / HLOOKUP
```excel
=VLOOKUP(lookup_value, table_array, col_index_num, [range_lookup])
=VLOOKUP("Apple", A1:D10, 3, FALSE)        -- Exact match
=VLOOKUP("A", A1:D10, 3, TRUE)             -- Approximate match (sorted)

=HLOOKUP(lookup_value, table_array, row_index_num, [range_lookup])
```

### INDEX / MATCH
```excel
=INDEX(array, row_num, [column_num])       -- Value at position
=INDEX(A1:C10, 5, 2)                     -- Row 5, Column 2

=MATCH(lookup_value, lookup_array, [match_type])
=MATCH("Apple", A1:A10, 0)               -- Exact match (0)
=MATCH(50, A1:A10, 1)                    -- Less than or equal (1, sorted)
=MATCH(50, A1:A10, -1)                   -- Greater than or equal (-1, sorted desc)

-- Combined (recommended over VLOOKUP)
=INDEX(return_array, MATCH(lookup_value, lookup_array, 0))
```

### XLOOKUP / XMATCH (Excel 365)
```excel
=XLOOKUP(lookup_value, lookup_array, return_array, [if_not_found], [match_mode], [search_mode])
=XLOOKUP("Apple", A1:A10, C1:C10, "Not Found", 0, 1)

=XMATCH(lookup_value, lookup_array, [match_mode], [search_mode])
=XMATCH("Apple", A1:A10, 0, 1)
```

### OFFSET / INDIRECT
```excel
=OFFSET(reference, rows, cols, [height], [width])
=OFFSET(A1, 2, 3)                        -- Cell D3
=OFFSET(A1, 0, 0, 10, 3)                 -- Range A1:C10

=INDIRECT(ref_text, [a1])
=INDIRECT("A1")                           -- Value in A1
=INDIRECT("Sheet2!A1")                    -- Cross-sheet reference
=INDIRECT("R" & ROW() & "C" & COLUMN(), FALSE)  -- R1C1 style
```

### Other Reference Functions
```excel
=ADDRESS(row_num, column_num, [abs_num], [a1], [sheet_text])
=ADDRESS(2, 3)                            -- "$C$2"
=ADDRESS(2, 3, 4)                         -- "C2" (relative)

=CHOOSE(index_num, value1, value2, ...)
=CHOOSE(MONTH(TODAY()), "Jan", "Feb", "Mar", ...)

=TRANSPOSE(array)                          -- Switch rows/columns
=ROW([reference])                          -- Row number
=COLUMN([reference])                         -- Column number
=ROWS(array)                               -- Number of rows
=COLUMNS(array)                            -- Number of columns
=AREAS(reference)                          -- Number of areas
```

---

## Statistical Functions

### Basic Statistics
```excel
=AVERAGE(number1, number2, ...)            -- Arithmetic mean
=AVERAGEA(value1, value2, ...)             -- Mean including text/logical
=MEDIAN(number1, number2, ...)            -- Middle value
=MODE.SNGL(number1, number2, ...)          -- Most frequent value
=MODE.MULT(number1, number2, ...)          -- All modes (array)

=STDEV.S(number1, number2, ...)            -- Sample standard deviation
=STDEV.P(number1, number2, ...)           -- Population standard deviation
=VAR.S(number1, number2, ...)              -- Sample variance
=VAR.P(number1, number2, ...)             -- Population variance

=MIN(number1, number2, ...)                -- Minimum
=MAX(number1, number2, ...)                -- Maximum
=LARGE(array, k)                           -- Kth largest
=SMALL(array, k)                           -- Kth smallest
=PERCENTILE.INC(array, k)                  -- Kth percentile (0-1)
=PERCENTRANK.INC(array, x)                 -- Percentile rank of x
=QUARTILE.INC(array, quart)                -- Quartile (0-4)
```

### Counting
```excel
=COUNT(value1, value2, ...)                -- Count numbers
=COUNTA(value1, value2, ...)               -- Count non-empty
=COUNTBLANK(range)                         -- Count empty cells
=COUNTIF(range, criteria)                    -- Count matching condition
=COUNTIFS(criteria_range1, criteria1, ...)  -- Count matching multiple conditions
```

### Conditional Aggregates
```excel
=SUMIF(range, criteria, [sum_range])         -- Sum matching condition
=SUMIFS(sum_range, criteria_range1, criteria1, ...)  -- Sum matching multiple
=AVERAGEIF(range, criteria, [average_range]) -- Average matching condition
=AVERAGEIFS(average_range, criteria_range1, criteria1, ...)  -- Multiple conditions
=MAXIFS(max_range, criteria_range1, criteria1, ...)  -- Max matching conditions
=MINIFS(min_range, criteria_range1, criteria1, ...)  -- Min matching conditions
```

### Correlation & Regression
```excel
=CORREL(array1, array2)                    -- Correlation coefficient
=COVARIANCE.S(array1, array2)              -- Sample covariance
=COVARIANCE.P(array1, array2)             -- Population covariance
=SLOPE(known_y's, known_x's)               -- Linear regression slope
=INTERCEPT(known_y's, known_x's)           -- Linear regression intercept
=FORECAST.LINEAR(x, known_y's, known_x's)  -- Linear forecast
=FORECAST.ETS(target_date, values, timeline, [seasonality])  -- Exponential smoothing
=TREND(known_y's, [known_x's], [new_x's]) -- Linear trend values
=GROWTH(known_y's, [known_x's], [new_x's]) -- Exponential growth values
```

---

## Logical Functions

### IF Family
```excel
=IF(logical_test, value_if_true, value_if_false)
=IF(A1>100, "High", "Low")
=IF(A1>100, "High", IF(A1>50, "Medium", "Low"))  -- Nested IF

=IFS(logical_test1, value1, logical_test2, value2, ...)
=IFS(A1>=90, "A", A1>=80, "B", A1>=70, "C", A1>=60, "D", TRUE, "F")

=IFERROR(value, value_if_error)
=IFERROR(A1/B1, 0)

=IFNA(value, value_if_na)
=IFNA(VLOOKUP(A1, B1:D10, 2, FALSE), "Not Found")

=SWITCH(expression, value1, result1, value2, result2, ..., [default])
=SWITCH(A1, 1, "Jan", 2, "Feb", 3, "Mar", "Other")
```

### Boolean Functions
```excel
=AND(logical1, logical2, ...)              -- All must be TRUE
=OR(logical1, logical2, ...)               -- At least one TRUE
=NOT(logical)                              -- Reverse logical value
=XOR(logical1, logical2, ...)              -- Exclusive OR

=AND(A1>0, A1<100)                         -- Between 0 and 100
=OR(A1="Yes", A1="Y")                       -- Either Yes or Y
```

---

## Math & Trig Functions

### Basic Math
```excel
=ABS(number)                               -- Absolute value
=ROUND(number, num_digits)                 -- Round to N decimals
=ROUNDUP(number, num_digits)               -- Round up
=ROUNDDOWN(number, num_digits)             -- Round down
=MROUND(number, multiple)                  -- Round to nearest multiple
=CEILING(number, significance)             -- Round up to multiple
=FLOOR(number, significance)               -- Round down to multiple
=CEILING.MATH(number, [significance], [mode])  -- Excel 2013+
=FLOOR.MATH(number, [significance], [mode])    -- Excel 2013+
=INT(number)                               -- Integer part
=TRUNC(number, [num_digits])               -- Truncate decimals
=MOD(number, divisor)                        -- Remainder
=QUOTIENT(numerator, denominator)            -- Integer division
=POWER(number, power)                        -- Exponentiation
=SQRT(number)                                -- Square root
=EXP(number)                                 -- e^number
=LN(number)                                  -- Natural log
=LOG(number, [base])                         -- Logarithm
=LOG10(number)                               -- Base-10 log
```

### Random Numbers
```excel
=RAND()                                    -- Random 0 to 1
=RANDBETWEEN(bottom, top)                  -- Random integer
=RANDARRAY([rows], [columns], [min], [max], [integer])  -- Excel 365
```

### Trigonometry
```excel
=SIN(number), =COS(number), =TAN(number)   -- Basic trig
=ASIN(number), =ACOS(number), =ATAN(number) -- Inverse trig
=SINH(number), =COSH(number), =TANH(number) -- Hyperbolic
=DEGREES(angle)                            -- Radians to degrees
=RADIANS(angle)                            -- Degrees to radians
=PI()                                      -- Value of pi
```

---

## Information Functions

### Type Checking
```excel
=ISBLANK(value)                            -- Is empty
=ISNUMBER(value)                           -- Is number
=ISTEXT(value)                             -- Is text
=ISLOGICAL(value)                          -- Is TRUE/FALSE
=ISERROR(value)                            -- Is any error
=ISERR(value)                              -- Is error (not #N/A)
=ISNA(value)                               -- Is #N/A
=ISFORMULA(reference)                        -- Contains formula
=ISREF(value)                              -- Is reference
=ISODD(number), =ISEVEN(number)            -- Odd/even check
```

### Cell Information
```excel
=CELL(info_type, [reference])
=CELL("filename")                          -- Full path and sheet name
=CELL("row", A1)                           -- Row number
=CELL("col", A1)                           -- Column number
=CELL("format", A1)                        -- Number format

=INFO(type_text)
=INFO("osversion")                         -- Operating system
=INFO("recalc")                            -- Recalculation mode
```

---

## Dynamic Array Functions (Excel 365)

### Spill Functions
```excel
=FILTER(array, include, [if_empty])
=FILTER(A1:C10, B1:B10>100)               -- Rows where B > 100
=FILTER(A1:C10, (B1:B10>100)*(C1:C10<50)) -- Multiple conditions (AND)
=FILTER(A1:C10, (B1:B10>100)+(C1:C10<50)) -- Multiple conditions (OR)

=SORT(array, [sort_index], [sort_order], [by_col])
=SORT(A1:C10, 2, -1)                       -- Sort by column 2 descending

=SORTBY(array, by_array1, [sort_order1], ...)
=SORTBY(A1:C10, B1:B10, -1, C1:C10, 1)    -- Sort by B desc, then C asc

=UNIQUE(array, [by_col], [exactly_once])
=UNIQUE(A1:A100)                           -- Unique values
=UNIQUE(A1:A100, FALSE, TRUE)              -- Values appearing exactly once

=SEQUENCE(rows, [columns], [start], [step])
=SEQUENCE(10)                              -- 1 to 10
=SEQUENCE(5, 3)                            -- 5x3 grid
=SEQUENCE(12, 1, DATE(2024,1,1), 31)       -- Monthly dates

=RANDARRAY([rows], [columns], [min], [max], [integer])
=RANDARRAY(10, 1, 1, 100, TRUE)            -- 10 random integers 1-100
```

### Lookup Functions
```excel
=XLOOKUP(lookup_value, lookup_array, return_array, [if_not_found], [match_mode], [search_mode])
=XLOOKUP("Apple", A1:A10, C1:C10, "Not Found", 0, 1)
=XLOOKUP("Apple", A1:A10, C1:C10, , -1)    -- Wildcard match
=XLOOKUP("Apple", A1:A10, C1:C10, , , -1)  -- Search last to first

=XMATCH(lookup_value, lookup_array, [match_mode], [search_mode])
=XMATCH("Apple", A1:A10, 0, 1)             -- Exact match
```

### Advanced Functions
```excel
=LET(name1, value1, name2, value2, ..., calculation)
=LET(x, 10, y, 20, x + y)                  -- Returns 30
=LET(data, A1:A100, avg, AVERAGE(data), FILTER(data, data > avg))

=LAMBDA([parameter1, parameter2, ...], calculation)
=LAMBDA(x, x^2)(5)                         -- Returns 25
-- Define as named function:
-- Name: SQUARE, Refers to: =LAMBDA(x, x^2)
-- Usage: =SQUARE(5)

=BYROW(array, LAMBDA(row, calculation))
=BYROW(A1:C10, LAMBDA(row, SUM(row)))     -- Sum each row

=BYCOL(array, LAMBDA(column, calculation))
=BYCOL(A1:C10, LAMBDA(col, AVERAGE(col))) -- Average each column

=MAP(array1, [array2, ...], LAMBDA(parameter, calculation))
=MAP(A1:A10, B1:B10, LAMBDA(a, b, a + b))  -- Element-wise addition

=REDUCE(initial_value, array, LAMBDA(accumulator, value, calculation))
=REDUCE(0, A1:A10, LAMBDA(a, b, a + b))    -- Sum (like SUM)

=SCAN(initial_value, array, LAMBDA(accumulator, value, calculation))
=SCAN(0, A1:A10, LAMBDA(a, b, a + b))     -- Running total

=MAKEARRAY(rows, columns, LAMBDA(row, column, calculation))
=MAKEARRAY(3, 3, LAMBDA(r, c, r * c))     -- Multiplication table

=DROP(array, rows, [columns])
=DROP(A1:D10, 1, 1)                        -- Remove first row and column

=TAKE(array, rows, [columns])
=TAKE(A1:D10, 5, 3)                        -- First 5 rows, 3 columns

=EXPAND(array, rows, [columns], [pad_with])
=EXPAND(A1:C5, 10, 5, "N/A")               -- Expand with padding

=TOCOL(array, [ignore], [scan_by_column])
=TOCOL(A1:C3)                              -- Convert to single column

=TOROW(array, [ignore], [scan_by_column])
=TOROW(A1:C3)                              -- Convert to single row

=WRAPROWS(array, wrap_count, [pad_with])
=WRAPROWS(SEQUENCE(12), 4)                 -- 3x4 matrix

=WRAPCOLS(array, wrap_count, [pad_with])
=WRAPCOLS(SEQUENCE(12), 3)                 -- 4x3 matrix

=CHOOSEROWS(array, row_num1, [row_num2, ...])
=CHOOSEROWS(A1:D10, 1, 3, 5)               -- Select specific rows

=CHOOSECOLS(array, col_num1, [col_num2, ...])
=CHOOSECOLS(A1:D10, 1, 3)                  -- Select specific columns

=HSTACK(array1, [array2, ...])
=HSTACK(A1:A10, B1:B10)                    -- Horizontal append

=VSTACK(array1, [array2, ...])
=VSTACK(A1:A10, B1:B10)                    -- Vertical append
```

---

## Power Query M Language Quick Ref

### Basic Operations
```m
let
    Source = Excel.CurrentWorkbook(){[Name="Table1"]}[Content],
    ChangedType = Table.TransformColumnTypes(Source, {{"Date", type date}, {"Amount", type number}}),
    FilteredRows = Table.SelectRows(ChangedType, each [Amount] > 100),
    SortedRows = Table.Sort(FilteredRows, {{"Date", Order.Ascending}}),
    Grouped = Table.Group(SortedRows, {"Category"}, {{"Total", each List.Sum([Amount]), type number}}),
    AddedColumn = Table.AddColumn(Grouped, "Avg", each [Total] / Table.RowCount(Grouped), type number)
in
    AddedColumn
```

### Common Transformations
```m
-- Filter
Table.SelectRows(table, each [Column] > 100)
Table.SelectRows(table, each Text.Contains([Name], "Apple"))

-- Select columns
Table.SelectColumns(table, {"Col1", "Col2"})
Table.RemoveColumns(table, {"Col3", "Col4"})

-- Rename
Table.RenameColumns(table, {{"OldName", "NewName"}})

-- Change type
Table.TransformColumnTypes(table, {{"Col", type number}})

-- Add column
Table.AddColumn(table, "NewCol", each [Col1] + [Col2], type number)

-- Remove duplicates
Table.Distinct(table)
Table.Distinct(table, {"Col1", "Col2"})

-- Sort
Table.Sort(table, {{"Col1", Order.Descending}, {"Col2", Order.Ascending}})

-- Group
Table.Group(table, {"Category"}, {{"Sum", each List.Sum([Amount]), type number}})
Table.Group(table, {"Category"}, {{"All", each _, type table}})

-- Pivot/Unpivot
Table.Pivot(table, List.Distinct(table[Attribute]), "Attribute", "Value", List.Sum)
Table.UnpivotOtherColumns(table, {"ID"}, "Attribute", "Value")

-- Merge (Join)
Table.NestedJoin(table1, {"Key"}, table2, {"Key"}, "Joined", JoinKind.LeftOuter)
Table.ExpandTableColumn(merged, "Joined", {"ColFromTable2"})

-- Append (Union)
Table.Combine({table1, table2})

-- Fill
Table.FillDown(table, {"Col"})
Table.FillUp(table, {"Col"})

-- Replace values
Table.ReplaceValue(table, null, 0, Replacer.ReplaceValue, {"Col"})
Table.ReplaceValue(table, each [Col], each Text.Upper([Col]), Replacer.ReplaceValue, {"Col"})

-- Split column
Table.SplitColumn(table, "Name", Splitter.SplitTextByDelimiter(" "), {"First", "Last"})

-- Promote headers
Table.PromoteHeaders(table)
Table.DemoteHeaders(table)
```

---

## DAX Quick Reference

### Aggregation Functions
```dax
SUM(column)
AVERAGE(column)
COUNT(column)
COUNTA(column)
COUNTBLANK(column)
COUNTROWS(table)
DISTINCTCOUNT(column)
MAX(column), MIN(column)
PRODUCT(column)
```

### Filter Functions
```dax
CALCULATE(expression, filter1, filter2, ...)
CALCULATE(SUM(Sales[Amount]), Sales[Year] = 2024)

FILTER(table, condition)
FILTER(Sales, Sales[Amount] > 1000)

ALL(table_or_column)              -- Remove all filters
ALLEXCEPT(table, column1, ...)    -- Remove all except specified
ALLSELECTED(table_or_column)      -- Keep external filters
VALUES(column)                    -- Unique values in current context
DISTINCT(column)                  -- Unique values (no blank)

RELATED(column)                   -- Lookup related table (many side)
RELATEDTABLE(table)               -- Lookup related table (one side)

EARLIER(column)                   -- Access previous row context
EARLIEST(column)                  -- Access outermost row context
```

### Time Intelligence
```dax
TOTALYTD(expression, dates)
TOTALQTD(expression, dates)
TOTALMTD(expression, dates)

SAMEPERIODLASTYEAR(dates)
PARALLELPERIOD(dates, number_of_intervals, interval)
DATEADD(dates, number_of_intervals, interval)

DATESYTD(dates)
DATESQTD(dates)
DATESMTD(dates)

PREVIOUSYEAR(dates)
PREVIOUSQUARTER(dates)
PREVIOUSMONTH(dates)
PREVIOUSDAY(dates)

NEXTYEAR(dates)
NEXTQUARTER(dates)
NEXTMONTH(dates)
NEXTDAY(dates)

FIRSTDATE(dates)
LASTDATE(dates)
FIRSTNONBLANK(column, expression)
LASTNONBLANK(column, expression)

DATEDIFF(date1, date2, interval)  -- DAY, MONTH, QUARTER, YEAR
```

### Logical Functions
```dax
IF(condition, value_if_true, value_if_false)
IF(Sales[Amount] > 1000, "High", "Low")

SWITCH(expression, value1, result1, value2, result2, ..., [else])
SWITCH(TRUE(), [Score] >= 90, "A", [Score] >= 80, "B", "F")

AND(condition1, condition2)
OR(condition1, condition2)
NOT(condition)

IFERROR(expression, value_if_error)
IFERROR(Sales[Amount] / Sales[Quantity], 0)

COALESCE(value1, value2, ...)     -- First non-blank
```

### Text Functions
```dax
CONCATENATE(text1, text2)
CONCATENATEX(table, expression, [delimiter])
CONCATENATEX(Products, Products[Name], ", ")

LEFT(text, num_chars)
RIGHT(text, num_chars)
MID(text, start_num, num_chars)
LEN(text)
FIND(find_text, within_text, [start_num])
SEARCH(find_text, within_text, [start_num])
SUBSTITUTE(text, old_text, new_text, [instance_num])
TRIM(text)
UPPER(text), LOWER(text), PROPER(text)
FORMAT(value, format_string)
FORMAT(1234.5, "$#,##0.00")
VALUE(text)
```

### Math Functions
```dax
ABS(number)
ROUND(number, num_digits)
MROUND(number, multiple)
CEILING(number, significance)
FLOOR(number, significance)
INT(number)
TRUNC(number, [num_digits])
DIVIDE(numerator, denominator, [alternate_result])
DIVIDE(Sales[Amount], Sales[Quantity], 0)
SQRT(number)
POWER(number, power)
EXP(number)
LN(number)
LOG(number, [base])
LOG10(number)
PI()
```

---

## Keyboard Shortcuts

### Navigation
| Shortcut | Action |
|----------|--------|
| Ctrl + Home | Go to A1 |
| Ctrl + End | Go to last used cell |
| Ctrl + Arrow | Jump to edge of data |
| Ctrl + Shift + Arrow | Select to edge of data |
| Ctrl + Page Up/Down | Switch worksheets |
| Ctrl + G / F5 | Go To dialog |
| F5 + Enter | Go to named range |

### Editing
| Shortcut | Action |
|----------|--------|
| F2 | Edit cell |
| F4 | Cycle reference types |
| Ctrl + ; | Insert current date |
| Ctrl + Shift + ; | Insert current time |
| Ctrl + D | Fill down |
| Ctrl + R | Fill right |
| Ctrl + Enter | Fill selected cells with entry |
| Alt + Enter | New line in cell |
| Ctrl + Z | Undo |
| Ctrl + Y | Redo |

### Formatting
| Shortcut | Action |
|----------|--------|
| Ctrl + 1 | Format Cells dialog |
| Ctrl + B | Bold |
| Ctrl + I | Italic |
| Ctrl + U | Underline |
| Ctrl + Shift + $ | Currency format |
| Ctrl + Shift + % | Percentage format |
| Ctrl + Shift + # | Date format |
| Ctrl + Shift + ! | Number format |
| Ctrl + Shift + & | Add borders |
| Ctrl + Shift + _ | Remove borders |

### Formulas & Functions
| Shortcut | Action |
|----------|--------|
| F3 | Paste name |
| F9 | Calculate selected |
| Shift + F3 | Insert Function dialog |
| Ctrl + ` | Toggle formula display |
| Ctrl + Shift + A | Insert function arguments |
| Alt + = | AutoSum |

### Data & Tables
| Shortcut | Action |
|----------|--------|
| Ctrl + T | Create Table |
| Ctrl + Shift + L | Toggle AutoFilter |
| Alt + D + S | Sort dialog |
| Alt + A + Q | Advanced Filter |
| Alt + D + P | PivotTable wizard |
| F11 | Create chart on new sheet |
| Alt + F1 | Create chart on current sheet |

### General
| Shortcut | Action |
|----------|--------|
| Ctrl + N | New workbook |
| Ctrl + O | Open |
| Ctrl + S | Save |
| Ctrl + P | Print |
| F12 | Save As |
| Ctrl + F | Find |
| Ctrl + H | Replace |
| Ctrl + A | Select all |
| Ctrl + C | Copy |
| Ctrl + V | Paste |
| Ctrl + X | Cut |
| Ctrl + Shift + V | Paste Special |
| F1 | Help |
| F7 | Spell check |
