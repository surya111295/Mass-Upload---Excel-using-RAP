# Mass-Upload---Excel-using-RAP
Mass Upload Data from Excel Sheet into RAP Application 

Features : Cloud Compliant,
           100% Clean-Core,
           Fully RAP managed - No UI5 Workaround,
           Mass Upload

Business Scenario : A database table created with Supplier Details, The records should be updated from an excel file.


Technique Used : RAP - Managed Scenario,
                 ODATA V4,
                 Abstract Entity with Association,
                 Custom Action,
                 XCO API

Application Demo

1. Excel File Contents

<img width="1292" height="200" alt="image" src="https://github.com/user-attachments/assets/67adb5ee-0c11-4450-a846-8dca32a3bb06" />

2. App Preview

<img width="1009" height="390" alt="image" src="https://github.com/user-attachments/assets/3b6b0dcc-1120-4e5f-8016-b59d64c901e1" />

3. Upload Excel

<img width="415" height="265" alt="image" src="https://github.com/user-attachments/assets/23765a57-9ee0-47b9-af2e-e458d4c34bf4" />

4. Successful upload of Records into DB Table

<img width="986" height="464" alt="image" src="https://github.com/user-attachments/assets/41d743cc-5c8b-42aa-bc20-bad6ea9304a3" />


List of Technical Objects

<img width="1049" height="713" alt="image" src="https://github.com/user-attachments/assets/b4779bbc-1570-416e-93b0-441f86101d72" />


Lessons Learnt:

1. When we create a behaviour definition with managed Application, create, Update button is not shown by default. How to resolve this?
Sol : 1. Always create the Interface BD with Draft as mandatory when ODATA V4 is selected
      2. Always expose the Draft with Actions on the Projection BD
2. How to make the Action Button to upload the Excel ?
Sol :  1. Create the File Stream Abstract Entity First
       2. Create the Abstract Entity to link this File Stream as an association
3. How to use XCO library to read the data?
Sol :
           1. Read the Excel.
           data(lo_xlsx) = xco_cp_xlsx=>document->for_file_content( iv_file_content = lv_attachment )->read_access( ).
           2. Access the Worksheet
           data(lo_worksheet) = lo_xlsx->get_workbook( )->worksheet->at_position( 1 ).
           3. Read the data from Columns and Rows
               data(lo_sel_pattern) = xco_cp_xlsx_selection=>pattern_builder->simple_from_to(
                 )->from_column( xco_cp_xlsx=>coordinate->for_alphabetic_value( 'A' )  " Start reading from Column A
                 )->to_column( xco_cp_xlsx=>coordinate->for_alphabetic_value( 'G' )   " End reading at Column N
                 )->from_row( xco_cp_xlsx=>coordinate->for_numeric_value( 2 )    " *** Start reading from ROW 2 to skip the header ***
                 )->get_pattern( ).
           4. Execute the File
                lo_worksheet->select( lo_sel_pattern
                                     )->row_stream(
                                     )->operation->write_to( REF #( lt_local    )
                                     )->set_value_transformation(
                                         xco_cp_xlsx_read_access=>value_transformation->string_value
                                     )->execute( ).
4. What is the EML statement used?
Sol : MODIFY ENTITIES OF zi_supplier_view IN LOCAL MODE
           ENTITY zb_supplier
           CREATE AUTO FILL CID FIELDS ( supplier_name
                                         address_id
                                         plant_id
                                         city
                                         country
                                         supplier_material
                                         supplier_price
                                         supplier_currency )
           WITH lt_listing_create
           " TODO: variable is assigned but never used (ABAP cleaner)
           MAPPED DATA(lt_mapped)
           " TODO: variable is assigned but never used (ABAP cleaner)
           REPORTED DATA(lt_reported)
           " TODO: variable is assigned but never used (ABAP cleaner)
           FAILED DATA(lt_failed).





