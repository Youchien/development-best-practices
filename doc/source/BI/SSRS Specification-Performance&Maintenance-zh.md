# SQL Server Reporting Services 开发规约（性能与维护）


## 1: 使用一个通用的SSRS模板

如果可能，创建一个通用的SSRS模板，并将其保存到具有只读属性的文件系统中，这样就没有人可以修改此通用模板。使用此通用报表模板生成所有新报表;这有助于大家生成的report的风格统一。该通用模板可以包括报表标题中的组织标识、地址、报表执行时间和页码、报表页脚中的confidential信息等。

如果不使用通用模板进行报表，则:

  * 对报表性能的影响 - 无
  * 对报表维护的影响 - 高


## 2: 使用共享SSRS数据源连接

你可以在报表中创建嵌入式数据源连接;这样做没有问题，但是如果您的数据库被移动到一个新的服务器上怎么办;在这种情况下，你必须修改每个报表中的数据源连接。假设一个项目中有20个报表指向相同的数据库;然后您必须修改所有20个报表中的数据源连接。更改所有20个报表中的数据源连接不是一个好主意，因为你必须在更改之后部署所有20个报表，这将花费大量时间和精力。可以创建项目级共享数据源，并在每个报表中使用它。如果数据源连接中发生任何更改，一个更改可以同时修复所有20个报表。

如果不适用共享数据源连接，则:

  * 对报表性能的影响 - 无
  * 对报表维护的影响 - 高


## 3: 不要在SSRS中创建不必要的数据集(datasets)

如果可能，避免为你的报表创建额外的数据集;即使报表中没有使用每个数据集，也会执行它。可以将一个数据集用于多个报表部分(即Tablix、Matrix、Chart)；因此，看看是否可以将两个或多个数据集合并为一个数据集。如果一个参数的值是固定的，那么尝试指定这些参数值，并避免数据集获取参数值(例如，如果您的报表有一个月参数，则月号是固定的)，不要从数据库中获取月号。如果参数有固定的默认值，那么指定默认值，避免使用数据集作为参数默认值。

如果在报表中创建了不必要的数据集，则:

  * 对报表性能的影响 - 高
  * 对报表维护的影响 - 高


## 4: 不要检索超过所需的数据

检查所有数据集返回的记录条数，确保它不是太大。不需要的数据不应该被检索。建议不要在数据集查询中使用SELECT * 语句，总是在查询中指定列名，只提取报表所需的那些列。

如果在报表中检索超过所需的数据，则:

  * 对报表性能的影响 - 高
  * 对报表维护的影响 - 中


## 5: 分析和优化SSRS数据集查询以获得更好的性能

对于你的数据集查询，遵循开发规约并尝试使用最优化的查询总是好的。你可以使用SQL Profiler和执行计划来分析数据集查询，并查看哪里可以改进查询。


## 6: 避免在SSRS的报表级别进行数据过滤

如果可能，使用WHERE子句在查询级别过滤数据，因为数据库更适合过滤数据。从数据库中提取所有数据，然后在报表级别对其进行过滤，这样做没有任何好处。当你没有其他选择,你不能修改数据集查询,那么你可以使用数据集过滤报表级别(例如,你有一个报表包含不止一个matrix/list/tablix/chart和他们共享相同的数据集),但必须过滤不同的输出(即一个tablix过滤客户和其他tablix过滤产品)。

如果在报表中进行了数据过滤，则:

  * 对报表性能的影响 - 高
  * 对报表维护的影响 - 中


## 7: 避免在SSRS报表级别进行字段计算

如果报表中有计算字段，那么最好在查询级别而不是报表级别创建它们，因为数据库可以更好地处理计算。这种方法还可以减少报表服务器上的报表处理负载，并简化维护。

如果在报表中创建新字段，则:

  * 对报表性能的影响 - 中
  * 对报表维护的影响 - 中


## 8: 将SSRS中的复杂查询转换为视图

考虑将复杂查询转换为视图，并从视图中选择生成报表的结果。这将有助于报表维护。


## 9: 避免在SSRS报表级别进行数据转换

在可能的情况下，在查询级别执行数据转换;它将提高报表性能并减少维护时间。

假设字符串数据类型存储为日期值。如果需要执行date操作为报表创建三个不同的值，则必须将该值转换三次。如果在查询级别执行数据转换，则可以直接执行date操作以获得三个新值。这将减轻报表服务器的负担，并使其更易于维护。

如果在报表中进行了数据转换，则:

  * 对报表性能的影响 - 中
  * 对报表维护的影响 - 中


## 10: 在T-SQL查询级别对数据进行排序

在数据源上对大量数据进行排序比在报表处理期间更有效。在查询中使用ORDER BY子句可以在查询级别对数据进行预排序。预存数据由于存储在内存中的方式而提高了报表处理时间。子组中的数据是自动排序的，如果不需要排序，可以删除tablix/matrix数据区域中的排序表达式。

如果数据在报表中排序，则:

  * 对报表性能的影响 - 中
  * 对报表维护的影响 - 中


## 11: 设置SSRS报表宽度

在开始报表之前，将报表宽度属性设置为标准纸张大小之一。这将使您了解可以显示的最大大小。


## 12: 限制SSRS中的参数数量

只使用必要的参数，因为参数越多，用户体验越差。如果希望按日期筛选数据，建议使用日历日期选择器，而不是为年、月和日设置三个不同的参数。对于需要大量参数的报表，您可能希望提前使用这些参数填充报表缓存。


## 13: 在报表服务中设置参数默认值

建议为每个参数设置默认值;如果默认值是报表大部分时间都将执行的值，那么也更好。如果所有参数都设置了默认值，那么在预览时将执行报表。


## 14: 对每个参数使用正确的数据类型

参数化查询;参数数据类型必须与底层数据库列匹配，否则可能需要隐式转换，这会对性能产生负面影响。如果参数数据类型不匹配，那么您的参数查询将运行缓慢并影响整个报表。建议对日期参数使用DateTime数据类型，而不是使用字符串，因为它允许用户从日历日期选择器中选择日期值。

如果没有使用正确的参数数据类型，则:

  * 对报表性能的影响 - 高
  * 对报表维护的影响 - 无


## 15: 避免在SSRS报表中使用 TotalPage 函数

当报表由呈现物理页面(例如PDF或图像)分页扩展的布局呈现时，对全局字段TotalPages的引用会影响报表处理性能。


## 16: 避免在一个页面中显示所有数据

在一个页面上显示所有数据通常会导致性能问题。报表在完成报表处理和呈现之前不会显示任何数据。相反，使用分页符来显示数据。


## 17: 避免在报表服务中使用交互式排序

避免使用交互式排序按钮，除非用户需要更改报表中数据的排序顺序。


## 18: 避免空白页

确保你的报表不包含空白页;如果您的报表中有它们，那么找出原因并解决它，否则空白页面将增加报表呈现时间。


## 19: 避免报表服务中中使用子报表

子报表便于重用，但是当运行时中有许多子报表实例时，特别是在Tablix中，子报表的性能就不佳了。尽可能避免使用子报表。如果需要下钻报表，请考虑链接报表以满足您的需求。


## 20: 对于大型SSRS报表，使用分页符

如果报表包含大量数据，且没有分页符，则报表可以长时间运行，并且在呈现完成后将显示数据。对于大型报表，建议使用break。
