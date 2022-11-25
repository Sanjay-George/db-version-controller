# 💽 DB Version Controller
Idea and design for an application to version control databases like MySQL, MongoDB, etc. Basically a git-like add-on to version-control your DB 

## 💡 Motivation and Background study 
* Why version control your database: https://blog.codinghorror.com/get-your-database-under-version-control/
* DB version control and existing tools: https://www.dolthub.com/blog/2021-09-17-database-version-control/

### Data Structures
* Prolly trees (Dolt blog): https://www.dolthub.com/blog/2020-04-01-how-dolt-stores-table-data/
* Merkle trees
* B-Tree

### ⚒️ Existing tools
* [Dolt - A version controlled DB](https://docs.dolthub.com/introduction/what-is-dolt)
* [Tools listed by Leon Babmrick](https://secretgeek.net/dbcontrol)
* [DBMS Tools - DB Version control tools](https://dbmstools.com/categories/version-control-tools)


## 📝 Requirements

### Share data across machines of developers
There should be a provision for developers to share data from tables or databases to others. This easily sets up development data on a new devs' machine.

### Merge data across machines
There should be a provision to merge data from one machine to another, just like with Git. This involves having to **resolve conflicts** (ID conflicts) and sometimes **cherry-picking** which data to merge and what to ignore.

This could ease the process of updating staging or testing environment with new valid test cases from local. Commonly followed approach for this is to maintain a list of queries and automatically run the queries on staging env. Or the QA team manually adds data on staging. Too much work!

### Version Control data in DB
There should be provision to commit each version of data, again like Git. This helps to **revert** to a previous version of the database incase of issues.

Local devs should be able to create their own **branches** of data and create PR against the master data for valid new test cases.

### Not a new DB, just an add-on
The tool should just be an add-on to latch onto your existing database, and not an entirely new database altogether. Just like [Git](https://git-scm.com/) works with any programming language or platform, this version-controller tool should work with any DB (SQL or NoSQL).


## 🎨 High level Design

![db-version-control](https://user-images.githubusercontent.com/10389062/204053829-d98d994b-b775-4c90-ac3b-472ab338b522.png)

### Components
1. **App:** This will be responsible for converting encrypted / encoded database files to text files containing DDL / DML statements and vice-versa. Human readable text files will be essential for version control. The app will also serve as a UI to resolve merge conflicts (similar to kdiff).
2. **DB Server:** MongoDB / MySQL / any other DB.
3. **DB data folder:** The path on disk where the DB stores its files and data. This depends on the database and OS used. 
4. **Sync files:** Folder containing human readable `.sql` or other type of files. This will be created by the application and is the folder that needs to be version controlled with Git.  

### Basic flow
Consider Bobby 🧔 and A lice 🐛 are developers trying to share their local data with each other. They use MySQL.

* Bobby creates a few tables, defining the schema, and adds basic data. He runs the `App` by pointing to the `DB data folder`, let's assume `/var/lib/mysql`.
* The `App` creates human-readable `.sql` files. One folder per database, and one file per table. 
* The folder created by the `App` is version controlled by Bobby (using `git init` command) and pushed to a **private** repo.
* The folder is pulled by A lice from the repo and is run through the `App`. 
* The `App` generates the `.ibd` and `.frm` files required for MySQL, which A lice copies to `var/lib/mysql`
* A lice now has the exact same copy of Bobby's database.

### Merge conflicts
Assume Bobby changed some data and wants to merge into master. 

* Bobby pulls the latest master
* He runs the `App` and points to the folder containing `sync files`. 
* The `App` shows conflicts between the changes on his machine at `/var/lib/mysql` and the master branch.
* Bobby chooses how to resolve the conflict using the UI of the `App`.
* Once resolved, he chooses to save the changes, which will modify the text files at the `Sync folder`.
* Git will now detect changes in the files corresponding to the tables that Bobby modified, and these changes can be commited and merged to master (create a PR first, ofc).



















<!-- ## 🤔 Use Cases -->

<!-- ### Scenario 1: Sharing data on dev environment
Consider Bobby 🧔 and A lice 🐛 are devs working on the next big thing. They need a simple process to share good test cases among each other.

**Potential solutions and underlying problems**

* **Send queries for DDL and DML to each other.**
1. Overhead in creating those queries.
2. IDs may vary between them, especially if auto-incremented IDs are used. In case of doc DBs like MongoDB, the ObjectIDs / UUIDs will differ. There are cases where having same IDs is necessary for coordination (enumerations, for example).

* **Use a common DB server (cloud or local network)**
1. Overhead in setting up server.
2. They could end up polluting each other's data

Neither of the solutions provide a way to revert to an earlier version of the DB (unless you take manual backups or log schema changes, which are hectic for small teams).  

### Scenario 2: Syncing data across environments (dev, staging, prod)
This is not just one scenario, but a set of scenarios:
1. Since dev data gets polluted quite quickly, staging data will need to be synced to dev periodically. Or a stable version of dev data will have to be restored.
2. Staging data needs to be updated at regular intervals to enable E2E tests and other forms of acceptance testing.
3. In some cases, data from staging / local might need to be updated on prod. (Consider enums or DDL statements).

Let's consider potential solutions and its downsides one by one:

* **Sync data from staging to dev by destroying all tables and recreating them**

This would entail having to maintain all DDL statements and DML for the appropriate test cases. An automated pipeline should take care of this, since manual efforts could lead to major mistakes. However, there are downsides to this approach:
1. Dev data would be replaced entirely by staging data. This means that developers who had created new data points for a new feature will have to spend time recreating them.
2. Syncing needs to be automated. Also needs to be error-proof.

* **Syncing staging data from previous backup and a list of new queries**

One way to sync staging data is to recreate everything from the latest backup or checkpoint, and then run new DDL, DML commands on top of them. This has downsides as well:
1. Developers will need to maintain the new DDL and DML statements. (error-prone)
2. Some sort of automation pipeline will need to be built for this.
 -->
