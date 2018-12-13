#SQL Server Reporting Services Best Practices for Report Design

## 1: Display parameter selection

It is always recommended to display the parameter values which are used to
filter the data in the report. If a report is exported and shared without
displaying the parameter values it will be a little difficult for the end user
to understand the data.

For detailed explanation please read this tip.


## 2: Don't wrap number and date data fields

Number and Date data fields are very important in reports. When number or date
data fields wrap into two or more lines it is difficult to read the data, so
always make sure your report has enough space to display the numbers and dates
without wrapping.


## 3: Use NoDataMessage property field

"NoRowsMessage" property is applicable to Table, Matrix, List data regions,
Subreports and Charts. When your dataset doesn't return any records the data
region will not display anything and it will be empty. It is recommended to
display text instead of displaying an empty data region. Use the NoDataMessage
property field to display a customized message for an empty dataset.


## 4: Use proper textbox orientation

Proper orientation of a Textbox saves a lot of space in the report layout,
which may help to display all of the data in a single page. It is recommended
to use proper textbox orientation.

For detailed explanation please read this tip.


## 5: Display date and time for last refreshed data

If your database is getting refreshed frequently by ETL or some other job, it
is recommended to display the last refresh date and time in the report. This
will help the user understand how current the data is that they are viewing.


## 6: Use uniform font type and size

Formatting is a very important part of report design. It is recommended to use
a uniform font type and size for all data elements like data fields and
textboxes in the report.


## 7: Don't make your report too colorful

Don't make your report too colorful, it may reduce visibility and lose focus on
important data. If you want the users attention for a specific dataset then you
should highlight only that section with different colors, fonts and styles.


## 8: Limit number of categories in a Pie chart

When the number of categories in the Pie Chart is too large it becomes very
difficult to identify which item on the legend corresponds to which slice of the
pie chart. It is recommended to have less categories when using pie charts. When
the number of categories is large it is recommended to use different types of
charts such as a bar chart or a line chart.

For detailed explanation please read this tip.


## 9: Separate smaller slices into a separate Pie chart

When you have a large number of categories and you are asked to display data
using a pie chart it is recommended to group all small slices into one category
and then you can separate the small slice group into a separate pie chart.

For detailed explanation please check this tip.


## 10: Split the report into multiple reports

In many cases you will find there are many data fields which are not so
important, but the report displays the data. The first thing you should do is
eliminate these extraneous data fields; it will help decrease the report size.
If the report is still returns a lot of data fields it is advised to split the
report into smaller chunks.


## 11: Alternate Row Background Color

The alternative row background color improves the readability of data in your
SQL Server Reporting Services (SSRS) Reports. It is not a must best practice,
but you can consider this if you are looking to improve readability.

For detailed explanation please read this tip.


## 12: Try Avoiding Lookup functions

Lookup functions are used to retrieve data from a second dataset at report time.
If possible try to join two tables at the query level, because Lookup functions
will be overhead on the Report Server if the call against the second dataset
might be lengthy. There might be situations when you can't write the query to
join the data, so you will have to use lookup functions.

If Lookup functions are used then:

    * Impact on Report Performance- High
    * Impact on Report Maintenance- Medium

For detailed information on Lookup functions, please check this tip.


## 13: Avoid aggregation at report time

Always do aggregation at the query level, because the database server can handle
these calculations better compared to the report server.

If aggregation is done at report level

    * Impact on Report Performance- High
    * Impact on Report Maintenance- Low


## 14: Avoid First, Last, Previous, RunningValue functions

These functions are more expensive to calculate than other aggregates, therefore
avoid if possible.


## 15: Export and check report readability

Once your report development is done, it is always recommend to export the
report into the desired format (i.e. Excel, PDF) and check for readability.
Always make sure your data fits on the page and meets user expectations.
