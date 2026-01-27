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
