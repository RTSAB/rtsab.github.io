---
layout: default
title:  "Calculating FTE with PowerBI"
parent: PowerBI
author: "Anders Nilsson"
nav_order: 1
---
## Introduction
{: .no_toc }

Let's say we have a table of project assignments consisting of assignees, project start and end date and an extent. With extent we mean the amount of time during the assignment, in percent, that an assignee is supposed to work on the project.

By using the DAX iterator function SUMX you can handle multiple assignees and assignments. You do it by first solving for the simple case, one assignee and one assignment, and then apply SUMX to iterate of multiple assignement and solve the task for the generic case. 

Our goal is to create a PowerBI-report that calculates the Full Time Equivalent, or FTE, of work that is going on in a given time period. For example, one person that is assigned 100% during a month is considered 1 FTE for that month. One person assigned 100% during the second half of that same month is considered 0.5 FTE. For multiple assignees you can sum the individual FTE to get a total FTE for a given period.

## Calculating Work Days
Since we only have a start and end date for the assignment, our first challenge is to figure out how many work days this is as we want to exclude weekends from the calculation.

When working with calendars in PowerBI it is best practice, and mostly required, to create a separate calendar table. 
This can easily be accomplished within PowerBI by clicking on Add Table and use the CALENDAR function in DAX:
```
Calendar = CALENDAR(FIRSTDATE('Assignments'[Start]), LASTDATE('Assignments'[End]))
```
This will create a table with dates starting at the earliest of our Assignments up until the end of the last.
Now we can go ahead and mark the dates with 1 for a work day and 0 for weekend. By doing this it will be very easy to calculate the number of work days in a period by just calculating the sum of work day.
Go ahead and add a new column to the calendar table with the following formula:
```
Work Day = if(WEEKDAY('Calendar'[Date], 2) < 6, 1, 0)
```
Based on your locale you might need to adjust the formula. This basically goes through each date and looks at the WEEKDAY as a number. In the example mon-fre is 0-5 and weekend 6 and 7. So if weekday is less than 6 we give Work Day a value of 1, otherwise it will be 0.
Now we have a simple way of calculating the number of work days in a given period.

## Assignment Days and Work Days in Period
For the next step we need to figure out two things. First, the number of assignment days in the period, and second, the corresponding number of workdays. We do this by creating a simple measure.
```
WorkDaysInPeriod = SUM('Calendar'[Work Day])
```
The SUM function adds all the numbers in in the specified column in the provided table filtered by context. That will give us a total of 5 working days in the chosen period (week) as shown in the diagram below.


![Calculate Sum](/assets/images/calculate_sum.png)

Calculating the Assigned Days is somewhat trickier because in addition to use the filter context we also need take in account when the assignment starts and ends. We start with a table called Assignments where we have one row with Start and End dates:
```
AssignedDays = CALCULATE(SUM('Calendar'[Work Day]), AND( 'Calendar'[Date] >= MIN(Assignments[Start]), 'Calendar'[Date] <= MAX(Assignments[End])))
```
Let's break it down. We use the CALCULATE function in DAX as it gives us control over the filter-context for our measure. So going backwards, ```AND( 'Calendar'[Date] >= MIN('Assignments'[Date]), 'Calendar'[Date] <= MAX('Assignments'[Date]))``` is our filter. As our input to this filter is from a table (Assignments), albeit with only one row, we need to use MIN/MAX to get only one value to compare with.
Now we can take this additionally filtered input and apply it to our previous function (SUM)

If we view this in a table for the month of july (juli in Swedish) we clearly see that AssignedDays has been filtered from the total period context and that we have only 5 days of a total of 21 assigned.

![Assigned Days](/assets/images/workday_calendar.png)

## Multiple Assignment Rows

Clearly this works for an Assignment table with only one row. But what happens if we add more assignments? The short answer is that we need to do the above calculation for AssignedDays for each row in the table. This is accomplished with an iterator function called SUMX:
```
SumAssignedDays = SUMX(Assignments, Assignments[AssignedDays])
```
The SUMX function will evaluate AssignedDays for each row in Assignment and give us a total of the Assignments we are looking at. Incidentally the SUM function we previously used is actually a simplified case of SUMX where we don't have to evaluate the row before summing it up. SUM will actually be transformed to a SUMX when the table is evaluated.
Another thing to note is that this new measure will work also for the situation where we only have one Assignment which means our AssignedDays measure is redundant and can be hidden.

## Calculate the FTE

Now that we have the Assigned Days and the WorkDays in Period we can calculate the FTE. Just as we did before, we need to do this in two steps. First we evaluate FTE for the single entry case:
```
FTE = DIVIDE(FIRSTNONBLANK(Assignments[Extent], 0) / 100 * [AssignedDays], [WorkDaysInPeriod])
```
Here we introduce two new functions. The first is DIVIDE which we use as it is safe for division by zero. FTE is calculated as Extent * AssignedDays / WorkDaysInPeriod and we get the input for Extent from a column. Hence we need to extract it as a value which we do by taking the FIRSTNONBLANK value. The 0 in the function is what we apply to any blank values. This will not actually be used but we have to put something there.
And finally, to make the generic case we create new measure with the SUMX function.
```
SumFTE = SUMX(Assignments, Assignments[FTE])
```

Our final table is now ready to be viewed. In our table we see two Users with different assignments. The first user is allocated 100% and the second 75%.

![Final Table](/assets/images/FTE_table.png)
