---
description: >-
  Describes the transform relation operator in the multi-stage query engine.
---

# Transform relational operator

The transform operator is used to apply a transformation to the input data.
They may filter out columns or add new ones by applying functions to the existing columns.
This operator is generated by the multi-stage query engine when you use a `SELECT` clause in a query, but
can also be used to implement other transformations.

## Implementation details
Transform operators apply some transformation functions to the input data received from upstream.
The cost of the transformation usually depends on the complexity of the functions applied, but comparing to other
operators, it is usually not very high.

### Blocking nature
The transform operator is a streaming operator. 
It emits the blocks of rows as soon as they are received from the upstream operator.


## Hints

None


## Stats
### executionTimeMs
Type: Long

The summation of time spent by all threads executing the operator.
This means that the wall time spent in the operation may be smaller that this value if the parallelism is larger than 1.

### emittedRows
Type: Long

The number of groups emitted by the operator.


## Explain attributes

The transform operator is represented in the explain plan as a `LogicalProject` explain node.

This explain node has a list of attributes that represent the transformations applied to the input data.
Each attribute has a name and a value, which is the expression used to generate the column.

For example:
```
LogicalProject(userUUID=[$6], deviceOS=[$4], EXPR$2=[SUBSTRING($4, 0, 2)])
  LogicalTableScan(table=[[default, userAttributes]])
```

Is saying that the output of the operator has three columns:
* `userUUID` is the 7th column in the virtual row projected by LogicalTableScan, which corresponds to the `userUUID`
  column in the table.
* `deviceOS` is the 5th column in the virtual row projected by LogicalTableScan, which corresponds to the `deviceOS`
  column in the table.
* `EXPR$2` is the result of the `SUBSTRING($4, 0, 2)` expression applied to the 5th column in the virtual row projected
  by LogicalTableScan. Given we know that the 5th column is `deviceOS`, we can infer that `EXPR$2` is the first two
  characters of the `deviceOS` column.


## Tips and tricks

None