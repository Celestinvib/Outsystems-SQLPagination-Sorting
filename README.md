# Outsystems-SQLPagination-Sorting
SQL Pagination/Sorting by Good practises Outsystems

 ![image](https://github.com/user-attachments/assets/bb8201ca-a84b-4282-8a74-b0b60b471698)


Solution working on a server/client model type of app


The sorting will be separed in two queries one ASC the other DESC to avoid using expand in line inputs using some boolean variable send it by the client

to know what query need to be executed know.
Server actions output variables like QueryList or TotalCount need it.
 

<i>Needed inputs:</i>
<ul>
 <li>@OrderBy  (Text)</li>
 <li>@StartIndex (Integer)</li>
 <li>@MaxRecords (Integer)</li>
</ul>

 
With this method is important to let the inputs set as expand in line no, to avoid SQL Injections
And have in mind that is structure based solution.

After where statement in case it's need it, add the following orderby section
This is an acceptable approach but unwanted exceptions can appear

<h3>[Not Recommended Order By]</h3>

    ORDER BY
    CASE 

        WHEN @OrderBy = 'x' THEN {Enitity}.[field]

        WHEN @OrderBy = 'y' THEN CAST({Enity}.[field2] AS INT) --this field is set as text but it's a number

    END

 

To avoid it, In case of error when combining fields different cases will solve the problem because unwanted conversation of the diffierent fields will be avoided

 

<H3>[Recommended Order By]</H3>

    ORDER BY
    CASE WHEN @OrderBy = 'Field1' THEN {Entity}.[Field1] END DESC,

    CASE WHEN @OrderBy = 'FieldCombined' THEN {Entity}.[Field2] + ' - ' + {Enity}.[Field3] END DESC,

    CASE WHEN @OrderBy = 'Field4' THEN CAST({Entity}.[Field4] AS INT) END DESC, --this field is set as text but it's a number

    --DESC (if need it)          

<h3>[Pagination after orderby]</h3>

OFFSET @StartIndex ROWS

FETCH FIRST @MaxRecords ROWS ONLY;

<h3><u>Good practises example OrderBy + Pagination ✔ </u></h3>

     ORDER BY
    CASE WHEN @OrderBy = 'Field1' THEN {Entity}.[Field1] END DESC,

    CASE WHEN @OrderBy = 'FieldCombined' THEN {Entity}.[Field2] + ' - ' + {Enity}.[Field3] END DESC,

    CASE WHEN @OrderBy = 'Field4' THEN CAST({Entity}.[Field4] AS INT) END DESC, --this field is set as text but it's a number
    OFFSET @StartIndex ROWS
    FETCH FIRST @MaxRecords ROWS ONLY;

<h3>[Query total count]</h3>

Needed variable <i>@TotalCount</i> & output structure with only an integer

<h4>Main Select without the orderby/pagination</h4>
  
        SELECT
        {Entity}.[Field1],
        {Entity}.[Field2] + '-' +  {Entity}.[Field3] as FieldCombined
        {Entity}.[Field4]

         FROM {Entity}
         WHERE  {Entity}.[Field2] = @SomeOtherInput

<h4>SELECT_COUNT (Get the total count of the select)</h4>


         SELECT COUNT (1) 
         FROM {Entity}
         WHERE  {Entity}.[Field2] = @SomeOtherInput
 

<h4>Visual Structure of the action:</h4>


![image](https://github.com/user-attachments/assets/56ba637c-4961-47ea-8e4b-ae4adb6efba7)




