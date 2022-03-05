---
title: ISO Week Date
description: "Ah sh*t, here we go again."
layout: post
toc: true
comments: true
image: https://imgs.xkcd.com/comics/formatting_meeting.png
categories: [iso8601, oracle]
---
# ISO Week Date

Handling date formats is probably the second-worst pain one can inflict upon a developer - the worst being handling time zones, of course (soon brother, soon). But **what say you is the week 01 day 01 of the year?** Participate in the poll below:

[LinkedIn Poll](https://www.linkedin.com/posts/shantam-maheshwari_iso8601-oracle-activity-6905759642908020736--O8V)

<!-- TODO: Twitter -->

![](https://imgs.xkcd.com/comics/formatting_meeting.png "Relevant XKCD")

Before we get to the answer, according to ISO 8601 (the gold standard) the correct way to format dates is YYYY-MM-DD. Makes sense, at least to anyone who's ever had to sort records by date (see URL - press `Ctrl + L` 🎅🏿).

![](https://imgs.xkcd.com/comics/iso_8601.png "Another one")

Ok, let's dive in!

## ISO 8601

### The What

> The ISO 8601 definition for week 01 is the week with the first Thursday of the Gregorian year (i.e. of January) in it.

![](https://c.tenor.com/xdOS4KcgiuwAAAAC/pale-tv.gif "I SO PALE")

### The How

Let's break it down to two simple steps.

<ol start="0">
  <li>Forget about 1 January for a moment.</li>
  <li>Find the first Thursday of the year.</li>
  <li>Find the Monday of the week (Monday to Sunday) containing it.</li>
</ol>

Here's an example. Let's find week 01 day 01 of the year 2022. Fire up the calendar on your system and follow along.

1. First Thursday of the year is 06-01-2022. (I'm using DD-MM-YYYY. Sue me!)
2. The Monday of the week containing it is 03-01-2022.

Hence, week 01 day 01 of 2022 is 3 January.

### The Implications (Part 1)

How about week 01 day 01 of the year 2020?

1. First Thursday of the year is 02-01-2020.
2. The Monday of the week containing it is 30-12-2019.

Yes, you that read right. The week 01 day 01 of the year 2020 is 30 December 2019!

### The Why

1. It is the first week with a majority (4 or more) of its days in January.
2. Its first day is the Monday nearest to 1 January.
3. It has the year's first working day in it, if Saturdays, Sundays and 1 January are not working days.

I personally find the first reason the most compelling.

### The Why Not

That is all good and dandy but... why not use 1 January? If you come to think of it, there aren't good enough excuses as to why we should. 
The reason the year shouldn't always start from 1 January is because then, for each year the weeks would start on a different day (the day 1 January falls on). 

## Story time

I recently developed a database partitioning tool at work (check out [my LinkedIn](https://www.linkedin.com/in/shantam-maheshwari/)). While creating weekly partitions, I found that Oracle Database provides two options for working with weeks, namely `WW` and `IW`. After some Google-fu, I came across this [enlightening discussion on AskTom](https://asktom.oracle.com/pls/apex/asktom.search?tag=week-of-year-in-sql-confusing).

The `trunc` function truncates dates to the option provided. For instance,
```sql
select trunc(to_date('05-MAR-2022'), 'MONTH') from dual;
-- 01-MAR-22
```

For weeks,
```sql
select trunc(to_date('01-JAN-2022'), 'WW') from dual;
-- 01-JAN-22
-- WW defines week 01 day 01 as 1 January

select trunc(to_date('01-01-2022'), 'IW') from dual;
-- 27-DEC-22
-- IW follows the ISO definition
```

To see the week number,
```sql
select to_char(trunc(to_date('01-01-2022')), 'IW') from dual;
-- 52
-- 01-01-2022 lies in week 52 of year 2021
```

### The Implications (Part 2)

```python
print(365/7, 365%7)
# 52.142857142857146 1

print(366/7, 366%7)
# 52.285714285714285 2
```

1. Going by `WW` or the 1 January definition, there will always be 53 weeks in a year (week 53 having 1 or 2 days).
2. Going by `IW` or the ISO definition, some years will have 52 weeks, some 53 (all weeks having 7 days).

## Further Reading

1. <https://en.wikipedia.org/wiki/ISO_week_date>
2. <https://en.wikipedia.org/wiki/ISO_8601>
