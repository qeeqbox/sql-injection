<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/sql-injection/main/sql-injection.png"></p>

A threat actor may alter structured query language (SQL) query to read, modify and write to the database or execute administrative commands for further chained attacks (The threat actor may get output or verbose errors while performing the injection)

## Example #1
1. Threat actor sends a malicious SQL query to a vulnerable target
2. The target process the query and return the result

## Code
#### Target Logic 
```js
...
app.post("/query", (request, response) => {
  const query = "SELECT * FROM users WHERE username = '"+ request.body.username +"' AND password = '"+ request.body.password +"'"
  connection.query(query, (error, result) => {
    response.json({"result":result,"err":error});
  });
});
...
```

#### Target-in
```
test';--
```

#### Target-Out
```
Welcome, test!
```

## Impact
High

## Names
- SQL injection

## Risk
- Read & write data
- Command execution

## Redemption
- Input validation
- Parametrized queries
- Stored procedures

## ID
85b0dbbc-1d39-4d96-9816-319276d2c0e2

## References
- [wiki](https://en.wikipedia.org/wiki/sql_injection)
