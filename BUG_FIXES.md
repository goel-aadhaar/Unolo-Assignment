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