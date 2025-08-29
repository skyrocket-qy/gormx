# gormx - GORM Enhance Plugin

[![Go Report Card](https://goreportcard.com/badge/github.com/skyrocketOoO/gormx)](https://goreportcard.com/report/github.com/skyrocketOoO/gormx)
[![Go.Dev](https://img.shields.io/badge/go.dev-reference-007d9c?logo=go&logoColor=white)](https://pkg.go.dev/github.com/skyrocketOoO/gormx)
[![license](https://img.shields.io/github/license/skyrocketOoO/gormx)](LICENSE)

`gormx` is a library that enhances the [GORM](https://gorm.io/) ORM for Go. It provides a set of utilities to make your database interactions safer, cleaner, and more expressive. The main goal of `gormx` is to eliminate raw strings from your GORM queries, providing type-safety and better developer experience.

## Features

- **Code Generation**: Automatically generate Go code for your table names and column names, eliminating the need for hardcoded strings.
- **Type-Safe Queries**: Write queries with generated code for compile-time checks and autocompletion.
- **Query Scopes**: A collection of reusable GORM scopes for common tasks like:
  - **Pagination**: Simple and consistent pagination.
  - **Filtering**: Declarative filtering with support for exact and fuzzy matching.
  - **Sorting**: Easy-to-use sorting for your query results.
- **Operator Constants**: A set of constants for SQL operators, making your code more readable and maintainable.

## Installation

To install `gormx`, use `go get`:

```bash
go get -u github.com/skyrocketOoO/gormx
```

## Usage

### Code Generation

`gormx` can generate Go code for your table and column names. This is a powerful feature that helps you avoid typos and makes your code more robust.

#### Table Names

To generate code for your table names, call `tablename.GenTableNamesCode`.

```go
package main

import (
	"github.com/skyrocketOoO/gormx/tablename"
	"gorm.io/driver/sqlite"
	"gorm.io/gorm"
)

func main() {
	db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
	if err != nil {
		panic("failed to connect database")
	}

	// Generate table name constants in the "generated/tablename" directory
	err = tablename.GenTableNamesCode(db, "generated/tablename")
	if err != nil {
		panic(err)
	}
}
```

This will generate a file with constants for each table in your database.

**Example Generated Code (`generated/tablename/tablename.go`):**
```go
package tablename

const (
    Users = "users"
    Products = "products"
)
```

**Usage in GORM:**
```go
import "generated/tablename"

db.Model(&User{}).Table(tablename.Users).First(&user)
```

#### Column Names

Similarly, you can generate code for your column names using `columnname.GenTableColumnNamesCode`.

```go
package main

import (
	"github.com/skyrocketOoO/gormx/columnname"
	"gorm.io/driver/sqlite"
	"gorm.io/gorm"
)

func main() {
	db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
	if err != nil {
		panic("failed to connect database")
	}

    // Specify which tables to generate column names for
    tableNames := []string{"users", "products"}

	// Generate column name structs in the "generated/columnname" directory
	err = columnname.GenTableColumnNamesCode(db, tableNames, "generated/columnname")
	if err != nil {
		panic(err)
	}
}
```

**Example Generated Code (`generated/columnname/columnname.go`):**
```go
package columnname

var Users = struct {
    ID string
    Name string
    Email string
}{
    ID: "id",
    Name: "name",
    Email: "email",
}
```

**Usage in GORM:**
```go
import "generated/columnname"

db.Where(? = ?, columnname.Users.Email, "test@example.com").First(&user)
```

### Query Scopes

`gormx` provides a set of GORM scopes to simplify common query tasks.

#### Pagination

Use `scope.ApplyPager` to easily paginate your results.

```go
import "github.com/skyrocketOoO/gormx/lib/scope"

pager := &scope.Pager{Number: 1, Size: 10}
var users []User
db.Scopes(scope.ApplyPager(pager)).Find(&users)
```

#### Filtering

Use `scope.ApplyFilter` to apply filters to your queries.

```go
import "github.com/skyrocketOoO/gormx/lib/scope"

filters := []scope.Filter{
    {Field: "name", Value: "jinzhu", Fuzzy: false},
    {Field: "email", Value: "@example.com", Fuzzy: true},
}
var users []User
db.Scopes(scope.ApplyFilter(db, filters)).Find(&users)
```

#### Sorting

Use `scope.ApplySorter` to sort your results.

```go
import "github.com/skyrocketOoO/gormx/lib/scope"

sorters := []scope.Sorter{
    {Field: "name", Asc: true},
    {Field: "created_at", Asc: false},
}
var users []User
db.Scopes(scope.ApplySorter(sorters)).Find(&users)
```

### Operator Constants

To make your code even cleaner, `gormx` provides constants for SQL operators.

```go
import (
    "github.com/skyrocketOoO/gormx/lib/operator"
    "github.com/skyrocketOoO/gormx/lib/where"
)

db.Where(where.B("age", operator.Gt), 18)
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
