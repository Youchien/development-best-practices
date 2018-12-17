# SQL Server Reporting Services Best Practices for Performance and Maintenance


## 1: Use a common SSRS template

When possible, create a common report template and save it to the file system
with the read only property, so that no one can modify the common template.
Use this common report template to generate all your new reports; it will
promote a uniform reporting experience. This common template may include an
organization logo, address, report execution time and page number in the
Report Header, confidential message in the Report Footer, etc.

If a common template is not used for reporting then:

  * Impact on Report Performance - Nothing
  * Impact on Report Maintenance - High


## 2: Use a shared SSRS data source connection

You can create embedded data source connections inside your report; there is not
a problem doing that, but what if your database is moved to a new server; in
this case you have to modify the data source connection inside each report.
Suppose you have 20 reports in one project that were pointing to the same
database; then you have to modify the data source connection in all 20 reports.
Changing the data source connection in all 20 reports will not be a good idea
because you have to deploy all 20 reports after the changes and it will take
a lot of time and effort. It's possible to create a project level Shared Data
Source and use it inside each report. If any changes occur in the data source
connection, one change can fix all 20 reports at once.

If shared data source connection is not used for reporting then:

  * Impact on Report Performance - Nothing
  * Impact on Report Maintenance - High


## 3: Don't create unnecessary datasets in SSRS

When possible, avoid creating extra datasets for your report; every dataset will
be executed even if it is not used in the report. You can use one dataset for
multiple report parts (i.e. Tablix, Matrix and Chart); so look to see if you can
combine two or more datasets into one dataset. If a parameter has fixed values
then try to specify those parameter values and avoid the dataset to get
parameter values (i.e. month numbers are fixed if your report has a month
parameter), and don't fetch month number from the database. If a parameter has
a fixed default value then specify the default value, avoid using a dataset
for a parameter default value.

If unnecessary datasets are created in reports then:

  * Impact on Report Performance - High
  * Impact on Report Maintenance - High


## 4: Don't retrieve more SQL Server data than is needed

Check the number of records returned by all datasets to make sure it is not too
big. Ask yourself if all data is needed for the report. Data which is not needed
should not be retrieved. It is advised not to use SELECT * statements in the
dataset query, always specify column names in the query and pull only those
columns which are required for the report.

If query retrieves more data than required then:

  * Impact on Report Performance - High
  * Impact on Report Maintenance - Medium


## 5: Analyze and tune the SSRS dataset query for better performance

It is always good to follow best practices for your dataset query and try to use
the most optimized query. You can use SQL Profiler and query plans to analyze
your dataset query and see where you can improve the query.


## 6: Avoid data filtering at the report level in SSRS

When possible, filter the data at the query level using a WHERE clause because
the database is better suited to filter data. There is no gain in pulling all of
the data from the database and later filtering it at the report level. When you
don't have any other option and you can't modify the dataset query then you
could use dataset filtering at the report level (i.e. you have a report
containing more than one matrix/list/tablix/chart and they share the same
general dataset), but the outputs must be filtered differently (i.e. one Tablix
is filtered on the customers and the other Tablix is filtered on the products).

If data filtering is done in report then:

  * Impact on Report Performance - High
  * Impact on Report Maintenance - Medium


## 7: Avoid calculated fields at the SSRS report level

If there are calculated fields in the report then it is better to create them at
the query level and not at the report level, because the database can better
handle the calculations. This approach will also promote less report processing
load on the report server and ease of maintenance.

If new fields created in report then:

  * Impact on Report Performance - Medium
  * Impact on Report Maintenance - Medium


## 8: Convert complex queries in SSRS into a View

Consider converting complex queries into Views and select from the View to
produce the results for the report. This will help with report maintenance.


## 9: Avoid data conversion at the SSRS report level

When possible perform data conversion at the query level; it will improve report
performance and reduce maintenance time.

Suppose a string data type is being stored as a date value. If you need to
perform date operations to create three different values for your report, you
will have to convert the value three times. If you performed the data conversion
at the query level then you could directly perform the date operation to get the
three new values. This would reduce the burden on the report server and would
make it easier to maintain.

If data conversion is done in the report then:

  * Impact on Report Performance - Medium
  * Impact on Report Maintenance - Medium


## 10: Sort data at the T-SQL query level

It is more efficient to sort large amounts of data at the data source than
during report processing. Using an ORDER BY clause in the query can presort the
data at the query level. Presorted data improves report processing time because
of the way it is stored in memory. Data within child groups is automatically
sorted, if you don't need sorting you can remove the sorting expression in the
data region of the tablix/matrix.

If data sorted in the report then:

  * Impact on Report Performance - Medium
  * Impact on Report Maintenance - Medium


## 11: Set the SSRS Report Width

Before starting the report, set the report width property to one of the standard
paper sizes. This will give you an idea of the maximum size you can display.


## 12: Limit number of parameters in SSRS

Use only necessary parameters, as more parameters will give a poor user
experience. If you want to filter data by date, it is recommended to use a
calendar date picker over having three different parameters for Year, Month and
Day. For reports that require a large number of parameters, you may want to look
at populating the report cache with those parameters ahead of time.


## 13: Set Parameter Default Values in Reporting Services

It is advised to set default values for each parameter; it is also better if the
default values are values for which the report will be executed most of the time.
If all the parameters have a default value set, then the report will be executed
when previewed.


## 14: Use correct data types for each parameter

For parameterized queries; the parameter data type must match the underlying
database column otherwise an implicit conversion may be needed, which can have a
negative performance impact. If there is a parameter data type mismatch then
your parameter query will run slow and impact the overall report. It is
recommended to use the DateTime data type for date parameters instead of using
strings, because it allows the user to pick the date value from a calendar date
picker.

If correct parameter data type is not used then:

  * Impact on Report Performance - High
  * Impact on Report Maintenance - Nothing


## 15: Avoid TotalPages function in SSRS reports

A reference to the global field TotalPages can affect report processing
performance when the report is rendered by a layout rendering the extension that
paginates for physical pages, for example, PDF or image.


## 16: Avoid displaying all data in a single page

Displaying all data on a single page will typically result in a performance
issue. The report will not display any data until it completes the report
processing and rendering. Instead, use page breaks to display the data.


## 17: Avoid using Interactive sorting in Reporting Services

Avoid interactive sort buttons unless users require the ability to change the
sort order of data in the report.


## 18: Avoid blank pages

Make sure your report doesn't contain blank pages; if your report has them then
figure out the reason and solve it, otherwise blank pages will increase report
rendering time.


## 19: Avoid sub reports in Reporting Services

Sub reports are convenient for reuse, but don't perform well when there are many
sub report instances during the runtime especially inside a Tablix. Try to avoid
the use of sub reports if possible. If drill down reports are needed, consider
linked reports to fulfill your requirement.


## 20: Use page breaks for large SSRS reports

If a report has a large amount of data and doesn't have page breaks, the report
can run for long time and the data will be displayed once rendering is complete.
It is advised to use break for large reports.
