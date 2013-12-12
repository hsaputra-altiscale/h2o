ScAlH2O
=======

Overview
--------
ScAlH2O is a Scala library providing access to H2O API via a dedicated DSL
and also a REPL integrated into H2O.

Currently the library supports following expressions abstracting H2O API:
```
R-like commands
      help
      ncol <frame>
      nrow <frame>
      head <frame>
      tail <frame>
      f(2)           - returns 2. column
      f("year")    - returns column "year"
      f(*,2)         - returns 2. column
      f(*, 2 to 5)   - returns 2., 3., 4., 5. columns
      f(*,2)+2       - scalar operation - 2.column + 2
      f(2)*3         - scalar operation - 2.column * 3
      f-1            - scalar operation - all columns - 1
      f < 10         - transform the frame into boolean frame respecting the condition

H2O commands
      keys              - shows all available keys i KV store
      parse("iris.csv") - parse given file and return a frame
      put("a.hex", f)   - put a frame into KV store
      get("b.hex")      - return a frame from KV store
      jobs              - shows a list of executed jobs
      shutdown          - shutdown H2O cloud

M/R commands
      f map (Add(3))   - call of map function of all columns in frame
                          - function is (Double=>Double) and has to extend Iced
      f map (Less(10)) - call of map function on all columns
                          - function is (Double=>Boolean) 
```

Launch REPL
-----------
The binary version of H2O with integrated Scala is accessible [here](s3.amazonaws.com/h2o-release/h2o/h2oscala/latest.html)
H2O offers command `-scala_repl` which launches embedded Scala REPL.

```bash
java -Xmx3G -jar target/h2o.jar -scala_repl
```

Key points
----------
- using specialization (to allow for generation code using primitive types)
- all objects passed around cloud has to inherits from `water.Iced`

Examples
--------
```scala
val f = parse("smalldata/cars.csv")

f(2)           // number of cylinders

f("year")      // year of production

f(*, 0::2::7)  // year,number of cylinders and year

f(7) map Sub(1000) // Subtract 1000 from year column

f("cylinders") map (new BOp { 
      var sum:scala.Double = 0
      def apply(rhs:scala.Double) = { sum += rhs; rhs*rhs / sum; } 
    })
```

ToDos
-----
- better typing
- better slicing and filtering
- API to access algos



