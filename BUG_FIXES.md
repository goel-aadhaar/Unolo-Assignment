***Bug 1: SQLite Errors***

**Locations** 
backend/routes/checkin.js
                line: 45,
                line: 88
backend/routes/dashboard.js
                line: 80

**Issue** 
Any field inside double quotes("xyz") gets treated as a column name, mongoDB style syntax being used line "DATE_SUB(NOW() INTERVAL 7 DAY)" 

**Fix**
Use single quotes('') for fields and back ticks(``) for queries

**Reason**
Using proper format makes proper comparision of values

***Bug 2: Request with wrong password being authenticated successfully***

**Location**
backend/routes/auth.js
                line: 28

**Issue**
bcrypt.compare returns a promise which is always a truthy value

**Fix**
Use await bcrypt.compare

**Reason**
After recieveing proper response syncronusly, the value will be assigned to 'isValidPassword'

***Bug 3: Users with correct data not being able to login***

**Location**
backend/routes/auth.js
                line: 11

**Issue**
No proper formatting of data before comparision

**Fix**
Trim the email data and convert to lowercase

**Reason**
Users can give email in mixed lowercase and uppercases but should be considered same

***Bug 4: History page Crash***

**Location**
frontend/pages/History.jsx 
                line: 5

**Issue**
'checkins' initially being null, reduce method can't be applied

**Fix**
replace null with empty array([])

**Reason**
reduce can be applied on an empty array without any issues

***Bug 5: Check Out functionality not working***

**Location**
backend/routes/checkin.js
                line: 88

**Issue**
MySQL style syntax being used

**Fix**
Replace NOW() with datetime('now)

**Resaon**
Query will properly work using correct SQLite syntax

***Bug 6: Check In functionality not working***

**Location**
backend/routes/checkin.js
                line: 57

**Issue**
Wrong column names being used

**Fix**
Replace 'lat' with 'latitude' and 'lng' with 'longitude'

**Reason** 
latitude and longitude are the actual column names of checkins table

***Bug 7: OK status code on wrong input***

**Location**
backend/routes/checkin.js
                line: 30

**Issue**
Incorrect Status Code for wrong input

**Fix** 
Use status code 400 instead of 200

**Reason**
Client will be getting a status code for wrong input

***Bug 8: SQL Injection Vulnerability***

**Location**
backend/routes/checkins.js
                line: 113,
                line: 116

**Issue**
Malicious code can be injected from start_date, end_date variables

**Fix**
Using Parametrized queries

**Reason**
through parameterized queries, data from variables is opened at the last stage where there is not risk of malicious activity

***Bug 9: Inconsistent types of latitude and longitude***

**Location**
database/schema.sql
                line: 45,
                line: 46

**Issue**
Two different datatypes Sring and Deciml causing issues

**Fix**
convert to DECIMAL 

**Reason**
values will remain consistent across tables

***Bug 10: Unrelible endpoint creation***

**Location**
frontend/checkin.js
            line: 15

**Issue**
risk of data leak

**Fix**
check for role instead of id

**Reason**
ID's can be changed in future , but not roles