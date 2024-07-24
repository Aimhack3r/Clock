# Clock

nput Files
1.	TENTRY (A):
o	File Name: /path/to/TENTRY.csv
o	Layout: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO, ENTRY_AMOUNT, TRANSACTION_CODE, CURRENCY_NUMBER.
2.	TACCOUNT (B):
o	File Name: /path/to/TACCOUNT.csv
o	Layout: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO, CLIENT_NUMBER, ACCOUNT_TYPE.
3.	TCUSTMA (C):
o	File Name: /path/to/TCUSTMA.csv
o	Layout: CLIENT_NUMBER, BRANCH_SORT_CODE.
4.	TCURREN (D):
o	File Name: /path/to/TCURREN.csv
o	Layout: CURRENCY_NUMBER, CCY_AMOUNT_FACTOR.
5.	TCALNOTE (E):
o	File Name: /path/to/TCALNOTE.csv
o	Layout: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO, SUB_PRODUCT_TYPE.
6.	TSUBPRD (F):
o	File Name: /path/to/TSUBPRD.csv
o	Layout: SUB_PRODUCT_TYPE, INTERNAL_PROD_IND.
Join Component
•	Join Keys:
o	TENTRY.ACCOUNT_NUMBER = TACCOUNT.ACCOUNT_NUMBER
o	TENTRY.ACCOUNT_SEQ_NO = TACCOUNT.ACCOUNT_SEQ_NO
o	TACCOUNT.CLIENT_NUMBER = TCUSTMA.CLIENT_NUMBER
o	TACCOUNT.CURRENCY_NUMBER = TCURREN.CURRENCY_NUMBER
o	TENTRY.ACCOUNT_NUMBER = TCALNOTE.ACCOUNT_NUMBER
o	TENTRY.ACCOUNT_SEQ_NO = TCALNOTE.ACCOUNT_SEQ_NO
o	TCALNOTE.SUB_PRODUCT_TYPE = TSUBPRD.SUB_PRODUCT_TYPE
Reformat Component
•	Transform Function:
plaintext
Copy code
out.DRORSIGN :: if (in.ENTRY_AMOUNT >= 0)
                 then 'CR'
                 else 'DR';
Filter Component
•	Filter Conditions:
plaintext
Copy code
in.BRANCH_SORT_CODE == 1203253 && in.ACCOUNT_TYPE == 'C' && in.INTERNAL_PROD_IND == 'N'
Rollup Component
•	Group By: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRORSIGN.
•	Aggregations:
o	COUNT(*)
o	SUM(ENTRY_AMOUNT)
o	CCY_AMOUNT_FACTOR
Output File
•	Target: /path/to/output.csv

Sure, I'll provide a detailed Ab Initio graph and the logic for each component based on the provided SQL extraction logic.

### Detailed Layout and Structure of the Ab Initio Graph

Here's a step-by-step layout and the logic for each component:

1. **Input Table Files (.dat.gz)**
   - **Component**: **Read Multiple Files**
     - **Description**: Reads the compressed input files.
     - **Details**: Specify the .dat.gz files for TENTRY, TACOUNT, TCUSTMR, TCURREN, TCALNOTE, and TSUBPRD.
     - **Files**:
       - TENTRY.dat.gz
       - TACOUNT.dat.gz
       - TCUSTMR.dat.gz
       - TCURREN.dat.gz
       - TCALNOTE.dat.gz
       - TSUBPRD.dat.gz

2. **Decompress**
   - **Component**: **Decompress**
     - **Description**: Decompress the input files to get the raw data.
     - **Details**: Use the appropriate decompression method for .dat.gz files.

3. **Parse Data**
   - **Component**: **Reformat**
     - **Description**: Parse the decompressed data into records with the appropriate schema.
     - **Details**: Define the record formats (schema) for TENTRY, TACOUNT, TCUSTMR, TCURREN, TCALNOTE, and TSUBPRD.
     - **Schemas**:
       - **TENTRY**: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO, TRANSACTION_CODE, ENTRY_AMOUNT
       - **TACOUNT**: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO, CLIENT_NUMBER, CURRENCY_NUMBER, ACCOUNT_TYPE
       - **TCUSTMR**: CLIENT_NUMBER, BRANCH_SORT_CODE
       - **TCURREN**: CURRENCY_NUMBER, CCY_AMOUNT_FACTOR
       - **TCALNOTE**: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO, SUB_PRODUCT_TYPE
       - **TSUBPRD**: SUB_PRODUCT_TYPE, INTERNAL_PROD_IND

4. **Join Data**
   - **Component**: **Join**
     - **Description**: Join the tables based on the specified keys.
     - **Details**:
       - Join TENTRY with TACOUNT on ACCOUNT_NUMBER and ACCOUNT_SEQ_NO
       - Join the result with TCUSTMR on CLIENT_NUMBER
       - Join the result with TCURREN on CURRENCY_NUMBER
       - Join the result with TCALNOTE on ACCOUNT_NUMBER and ACCOUNT_SEQ_NO
       - Join the result with TSUBPRD on SUB_PRODUCT_TYPE

5. **Filter Data**
   - **Component**: **Filter by Expression**
     - **Description**: Filter the joined data based on the conditions in the WHERE clause.
     - **Details**:
       - `C.BRANCH_SORT_CODE = '203253'`
       - `B.ACCOUNT_TYPE = 'C'`
       - `F.INTERNAL_PROD_IND = 'N'`

6. **Reformat and Transform Data**
   - **Component**: **Reformat**
     - **Description**: Reformat and apply transformations to the data, including the CASE logic for DR/CR signs.
     - **Details**:
       - Implement the CASE logic to determine DR/CR signs:
         ```plaintext
         CASE
           WHEN A.ENTRY_AMOUNT >= 0 THEN 'CR'
           WHEN A.ENTRY_AMOUNT < 0 THEN 'DR'
         END AS DRCRSIGN
         ```
       - Select the required columns: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN, ENTRY_AMOUNT, CCY_AMOUNT_FACTOR

7. **Group and Aggregate Data**
   - **Component**: **Rollup**
     - **Description**: Group by BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN and calculate COUNT(*) and SUM(ENTRY_AMOUNT).
     - **Details**:
       - Group by fields: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN
       - Aggregation functions: COUNT(*), SUM(ENTRY_AMOUNT)

8. **Output Data**
   - **Component**: **Output Table**
     - **Description**: Write the final aggregated data to an output table or file.
     - **Details**: Specify the output file path and schema.

### Visual Representation of the Graph

```plaintext
Input Table Files (.dat.gz)
    |
    V
Decompress Components
    |
    V
Parse Data Components (Reformat)
    |
    V
Join Components
    |
    V
Filter by Expression Component
    |
    V
Reformat Component
    |
    V
Rollup Component
    |
    V
Output Table Component
```

### Detailed Step-by-Step Logic for Each Component

1. **Read Multiple Files**
   - **Logic**: Read the compressed `.dat.gz` files for each table.
   - **Configuration**: Specify file paths for TENTRY.dat.gz, TACOUNT.dat.gz, TCUSTMR.dat.gz, TCURREN.dat.gz, TCALNOTE.dat.gz, and TSUBPRD.dat.gz.

2. **Decompress**
   - **Logic**: Decompress each `.dat.gz` file to obtain the raw data.
   - **Configuration**: Use a decompression utility (e.g., gunzip) to decompress the files.

3. **Parse Data (Reformat)**
   - **Logic**: Parse the decompressed raw data into structured records.
   - **Configuration**: Define the record format for each table:
     - TENTRY: ACCOUNT_NUMBER (string), ACCOUNT_SEQ_NO (string), TRANSACTION_CODE (string), ENTRY_AMOUNT (decimal)
     - TACOUNT: ACCOUNT_NUMBER (string), ACCOUNT_SEQ_NO (string), CLIENT_NUMBER (string), CURRENCY_NUMBER (string), ACCOUNT_TYPE (string)
     - TCUSTMR: CLIENT_NUMBER (string), BRANCH_SORT_CODE (string)
     - TCURREN: CURRENCY_NUMBER (string), CCY_AMOUNT_FACTOR (decimal)
     - TCALNOTE: ACCOUNT_NUMBER (string), ACCOUNT_SEQ_NO (string), SUB_PRODUCT_TYPE (string)
     - TSUBPRD: SUB_PRODUCT_TYPE (string), INTERNAL_PROD_IND (string)

4. **Join**
   - **Logic**: Perform joins between the parsed tables based on the specified keys.
   - **Configuration**:
     - Join TENTRY with TACOUNT on ACCOUNT_NUMBER and ACCOUNT_SEQ_NO
     - Join the result with TCUSTMR on CLIENT_NUMBER
     - Join the result with TCURREN on CURRENCY_NUMBER
     - Join the result with TCALNOTE on ACCOUNT_NUMBER and ACCOUNT_SEQ_NO
     - Join the result with TSUBPRD on SUB_PRODUCT_TYPE

5. **Filter by Expression**
   - **Logic**: Filter the joined data based on the specified conditions.
   - **Configuration**:
     - `C.BRANCH_SORT_CODE = '203253'`
     - `B.ACCOUNT_TYPE = 'C'`
     - `F.INTERNAL_PROD_IND = 'N'`

6. **Reformat (Transform Data)**
   - **Logic**: Apply transformations to the filtered data.
   - **Configuration**:
     - Implement the CASE logic to determine DR/CR signs:
       ```plaintext
       CASE
         WHEN A.ENTRY_AMOUNT >= 0 THEN 'CR'
         WHEN A.ENTRY_AMOUNT < 0 THEN 'DR'
       END AS DRCRSIGN
       ```
     - Select the required columns: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN, ENTRY_AMOUNT, CCY_AMOUNT_FACTOR

7. **Rollup (Group and Aggregate)**
   - **Logic**: Group by specified fields and perform aggregations.
   - **Configuration**:
     - Group by fields: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN
     - Aggregation functions: COUNT(*), SUM(ENTRY_AMOUNT)

8. **Output Table**
   - **Logic**: Write the final aggregated data to an output table or file.
   - **Configuration**: Specify the output file path and schema.

### Example Ab Initio Graph Components

Here's a conceptual representation of how each component might be configured in Ab Initio:

1. **Input Table Files Component**:
   ```plaintext
   Read Multiple Files
   Input Files: TENTRY.dat.gz, TACOUNT.dat.gz, TCUSTMR.dat.gz, TCURREN.dat.gz, TCALNOTE.dat.gz, TSUBPRD.dat.gz
   ```

2. **Decompress Component**:
   ```plaintext
   Decompress
   Input: *.dat.gz files
   Output: Decompressed data files
   ```

3. **Parse Data Components (Reformat)**:
   ```plaintext
   Reformat
   Input: Decompressed TENTRY file
   Output: Parsed TENTRY records
   Schema: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO, TRANSACTION_CODE, ENTRY_AMOUNT
   ```

4. **Join Component**:
   ```plaintext
   Join
   Input1: TENTRY
   Input2: TACOUNT
   Key: ACCOUNT_NUMBER, ACCOUNT_SEQ_NO
   Output: Joined records
   ```

5. **Filter by Expression Component**:
   ```plaintext
   Filter by Expression
   Condition: BRANCH_SORT_CODE = '203253' AND ACCOUNT_TYPE = 'C' AND INTERNAL_PROD_IND = 'N'
   ```

6. **Reformat Component**:
   ```plaintext
   Reformat
   Transformation: 
     DRCRSIGN = CASE
                  WHEN ENTRY_AMOUNT >= 0 THEN 'CR'
                  WHEN ENTRY_AMOUNT < 0 THEN 'DR'
                END
   Select: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN, ENTRY_AMOUNT, CCY_AMOUNT_FACTOR
   ```

7. **Rollup Component**:
   ```plaintext
   Rollup
   Group

 By: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN
   Aggregation: COUNT(*), SUM(ENTRY_AMOUNT)
   ```

8. **Output Table Component**:
   ```plaintext
   Output Table
   Output Path: /path/to/output/file
   Schema: BRANCH_SORT_CODE, ACCOUNT_NUMBER, TRANSACTION_CODE, DRCRSIGN, COUNT, SUM_ENTRY_AMOUNT, CCY_AMOUNT_FACTOR
   ```

This detailed layout and logic should help you implement the required Ab Initio graph for the given SQL extraction logic.