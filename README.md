# Simple Bank Application

This project is a **simple bank** application built using **Golang**, **PostgreSQL**, and **SQLC**. It offers core banking operations including creating accounts, tracking balance changes, and facilitating money transfers—all with proper transaction management and database consistency.

## Features

1. **Create and Manage Accounts**
   - Manage account details: **Owner, Balance, Currency**.
2. **Track Balance Changes**
   - Record every balance update by creating an **entry** per change.
3. **Money Transfer Transactions**
   - Perform consistent **money transfers** between two accounts, ensuring transactional integrity.

---

## Database Design

1. **DB Schema Design**

   - Design a relational schema for the banking system.
   - Create a **diagram** to visualize the relationships between tables.
   - **Generate SQL code** compatible with **PostgreSQL** as the target database engine.

2. **PostgreSQL Setup with Docker**

   - Install PostgreSQL using **Docker** to streamline deployment.

   ```bash
   docker run --name postgres-bank -e POSTGRES_PASSWORD=your_password -p 5432:5432 -d postgres
   ```

3. **Database Migration**
   - Use **golang-migrate** for easy migrations:
     ```bash
     go install -tags 'postgres' github.com/golang-migrate/migrate/v4/cmd/migrate@latest
     ```
   - Run **migrations**:
     ```bash
     migrate -path db/migrations -database "postgres://localhost:5432/simple_bank?sslmode=disable" up
     ```

---

## Generate Golang Code with SQLC

1. **SQLC Setup**

   - Install SQLC:
     ```bash
     go install github.com/kyleconroy/sqlc/cmd/sqlc@latest
     ```
   - Generate **CRUD operations** from SQL files:
     ```bash
     sqlc generate
     ```

2. **Benefits of SQLC**
   - **Fast and easy to use**: Avoid writing repetitive CRUD code.
   - **Automatic code generation**: Strongly-typed Go code generated from SQL queries.
   - **Error catching at compile-time**: Detect SQL errors before generating code.

---

## Unit Testing

1. **Test CRUD with Random Data**

   - Use **Golang testing framework** to write unit tests for CRUD operations.
   - Generate random test data to verify the correctness of each operation.

2. **Test Transactions**
   - Implement **DB transactions** and ensure their correctness with unit tests.
   - Benefits of Transactions:
     - **Reliable & consistent unit of work**: Ensure integrity even in system failure.
     - **Isolation for concurrent programs**: Prevent data races.
     - **ACID compliance**: Guarantee atomicity, consistency, isolation, and durability.

---

## Handle Deadlocks in Transactions

- Implement logic to **avoid** deadlocks and **recover** from them if they occur during concurrent transactions.
- Use **PostgreSQL's advisory locks** or retry mechanisms where applicable.

---

## CI Setup with GitHub Actions

1. **Automate Testing with GitHub Actions**

   - Use **Go + PostgreSQL** for automated tests in CI/CD.
   - Configure the CI **workflow** in a `.yml` file. Example:

     ```yaml
     name: ci-test

     on:
     push:
       branches: [master]
     pull_request:
       branches: [master]

     jobs:
     test:
       name: Test
       runs-on: ubuntu-latest
       services:
         postgres:
         image: postgres:alpine
         env:
           POSTGRES_USER: root
           POSTGRES_PASSWORD: postgres
           POSTGRES_DB: simple_bank
         ports:
           - 5432:5432
         options: >-
           --health-cmd pg_isready
           --health-interval 10s
           --health-timeout 5s
           --health-retries 5
         steps:
           - name: Set up Go 1.x
             uses: actions/setup-go@v2
             with:
               go-version: ^1.23.2
               id: go

           - name: Check out code into the Go module directory
             uses: actions/checkout@v2

           - name: Install golang-migrate
             run: |
               curl -L https://github.com/golang-migrate/migrate/releases/download/v4.18.1/migrate.linux-amd64.tar.gz | tar xvz
               sudo mv migrate /usr/bin/migrate
               which migrate

           - name: Run migrations
             run: make migrateup

           - name: Test
             run: make test
     ```

2. **Parallel Execution and Logging**
   - Configure tasks to run **in parallel** to speed up the workflow.
   - Report progress, logs, and results back to GitHub.

---

## Conclusion

This project demonstrates the development of a banking application using **Golang**, **PostgreSQL**, and **SQLC** with best practices in **database migrations**, **transaction handling**, and **continuous integration**.

---

### Getting Started

1. Clone the repository:

   ```bash
   git clone https://github.com/your-username/simple-bank.git
   cd simple-bank
   ```

2. Install dependencies:

   ```bash
   go mod download
   ```

3. Run migrations:

   ```bash
   make migrateup
   ```

4. Run tests:
   ```bash
   make test
   ```

---

### License

This project is licensed under the MIT License.

---

### Acknowledgments

- Thanks to **SQLC** and **golang-migrate** for making Go + PostgreSQL development seamless.
