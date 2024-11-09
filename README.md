# Outsystems-SQLPagination-Sorting
SQL Pagination/Sorting by Good practises Outsystems

 ![image](https://github.com/user-attachments/assets/bb8201ca-a84b-4282-8a74-b0b60b471698)


Solution working on a server/client model type of app


The sorting will be separed in two queries one ASC the other DESC to avoid using expand in line inputs using some boolean variable send it by the client

to know what query need to be executed know.
Server actions output variables like QueryList or TotalCount need it.

 

Needed inputs:

@OrderBy  (Text)

@StartIndex (Integer)

@MaxRecords (Integer)

 

After where statement in case it's need it

 

This is an acceptable approach but unwanted exceptions can appear

order by

    CASE

        WHEN @OrderBy = 'x' THEN {Enitity}.[field]

        WHEN @OrderBy = 'y' THEN CAST({Enity}.[field2] AS INT) --this field is set as text but it's a number

    END

 

To avoid it, In case of error when combining fields different cases will solve the problem because unwanted conversation of the diffierent fields will be avoided

 

order by

    CASE WHEN @OrderBy = 'Field1' THEN {Entity}.[Field1] END DESC,

    CASE WHEN @OrderBy = 'FieldCombined' THEN {Entity}.[Field2] + ' - ' + {Enity}.[Field3] END DESC,

    CASE WHEN @OrderBy = 'Field4' THEN CAST({Entity}.[Field4] AS INT) END DESC, --this field is set as text but it's a number

    --DESC (if need it)          

[Pagination after orderby]

OFFSET @StartIndex ROWS

FETCH FIRST @MaxRecords ROWS ONLY;

 

with this method is important to let the inputs set as expand in line no, to avoid SQL Injections

And have in mind that is structure based solution.

 

Good practises example:

 

order by

    CASE WHEN @OrderBy = 'Field1' THEN {Entity}.[Field1] END,

    CASE WHEN @OrderBy = 'FieldCombined' THEN {Entity}.[Field2] + ' - ' + {Enity}.[Field3] END,

    CASE WHEN @OrderBy = 'Field4' THEN CAST({Enituty}.[Field4] AS INT) END, --this field is set as text but it's a number

OFFSET @StartIndex ROWS

FETCH FIRST @MaxRecords ROWS ONLY;

 

[Query total count]

Needed variable TotalCount & output structure with only an integer

 

Normal select without the orderby/pagination

SELECT

{Entity}.[Field1],

{Entity}.[Field2] + ' - ' + {Enity}.[Field3] as FieldCombined,

{Entity}.[Field4]

from {Entity}

where

{Entity}.[Field1] = @someOtherInput

 

 

in the SELECT_COUNT (Get the total count of the select)

SELECT COUNT (1)

--rest of the select without sorting or pagination

from {Entity}

where

{Entity}.[Field1] = @someOtherInput

 

Visual Structure:


![image](https://github.com/user-attachments/assets/56ba637c-4961-47ea-8e4b-ae4adb6efba7)




