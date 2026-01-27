***Bug 1: SQLite Errors***

**Locations** 
backend/routes/checkin.js
                line: 45,
                line: 88
backend/routes/dashboard.js
                line: 80

**Issue** - Any field inside double quotes("xyz") gets treated as a column name, mongoDB style syntax being used line "DATE_SUB(NOW() INTERVAL 7 DAY)" 