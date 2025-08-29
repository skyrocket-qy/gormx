Generate the table name code to use without hard code table name

## How to generate code
```
import "github.com/skyrocketOoO/gormx/tablename"
tablename.GenTableNamesCode(db, path)
```

## How to use
![alt text](image.png)
```
var db *gorm.DB
db.Model(table.Roles)
```
