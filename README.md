# DB Version Controller
Idea and design for an application to version control databases like MySQL, MongoDB, etc.

## Background 

### Why version control your data?
https://blog.codinghorror.com/get-your-database-under-version-control/



## State of the art


## Requirements

### Share data across machines of developers
There should be a provision for developers to share data from tables or databases to others. This easily sets up development data on a new devs' machine.

### Merge data across machines
There should be a provision to merge data from one machine to another, just like with Git. This involves having to **resolve conflicts** (ID conflicts) and sometimes **cherry-picking** which data to merge and what to ignore.

This could ease the process of updating staging or testing environment with new valid test cases from local. Commonly followed approach for this is to maintain a list of queries and automatically run the queries on staging env. Or the QA team manually adds data on staging. Too much work!

### Version Control data in DB
There should be provision to commit each version of data, again like Git. This helps to **revert** to a previous version of the database incase of issues.

Local devs should be able to create their own **branches** of data and create PR against the master data for valid new test cases.



## Scenarios and usefulness

### Scenario 1: small teams and personal projects
Consider Bob 🧔 and Alice 👧 are devs working on the next big thing. They need a simple process to share good test cases among each other. 

**Potential solutions and underlying problems**

* **Send queries for DDL and DML to each other.**
1. Overhead in creating those queries.
2. IDs may vary between them, if auto-incremented IDs are used. In case of doc DBs like MongoDB, the ObjectIDs / UUIDs will differ. There are cases where having same IDs is necessary for coordination, especially with small teams.

* **Use a common DB server (cloud or local)**
1. Overhead in setting up server.
2. They could end up destroying each other's data
