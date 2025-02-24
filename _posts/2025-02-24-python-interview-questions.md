---
layout: post
category: technical
custom_js: mouse_coords
---
# Questionnaire for 5 year candidate

## 🐍 **Python Software Engineer – Evaluation Questionnaire**

### ✅ **Section 1: Core Python Knowledge**

1. **Explain Python’s memory management model.** How does garbage collection work?
2. **What are decorators and how do they work?** Provide a real-world use case.
3. **Describe the difference between `@staticmethod`, `@classmethod`, and instance methods.**
4. **What is a generator?** How does it differ from a regular function?
5. **Explain Python’s GIL (Global Interpreter Lock).** How does it impact multi-threaded applications?
6. **When would you use `__slots__` in a Python class?**
7. **What are context managers?** How does the `with` statement work internally?
8. **Explain list comprehensions vs. generator expressions.** When would you prefer one over the other?

---

### ✅ **Section 2: Advanced Python & Libraries**

1. **How would you handle concurrency in Python?** Compare threading, multiprocessing, and async programming.
2. **Given a large CSV file (5GB+), how would you process it efficiently in Python?**
3. **What are the differences between `deepcopy()` and `copy()`?**
4. **Have you used Pandas or NumPy?** Share a case where these libraries helped optimize performance.
5. **Explain how you would structure a Python package for a large-scale project.**
6. **How do you manage dependencies and virtual environments in Python?**

---

### ✅ **Section 3: System Design & Architecture**

1. **Design a RESTful API for a library management system.**
    - Outline the architecture, endpoints, and data model.
    - Consider authentication and rate-limiting.
2. **How would you scale a Python web application to handle 10,000 concurrent users?**
3. **What design patterns have you used in past projects?** Share real-world examples.
4. **Explain the difference between monolithic and microservices architectures.** When would you choose one over the other?
5. **Discuss the trade-offs between using SQL (PostgreSQL) vs. NoSQL (MongoDB) in a Python project.**

---

### ✅ **Section 4: Cloud, DevOps & Databases**

1. **Which cloud platforms (AWS, Azure, GCP) have you worked with?** Describe a Python project you deployed on the cloud.
2. **How would you implement CI/CD for a Python microservice?**
3. **Have you worked with Docker or Kubernetes?** How did they help in your project?
4. **What steps would you take to ensure security in a Python-based API (e.g., JWT, OAuth2)?**
5. **Explain how message queues (e.g., RabbitMQ, SQS) can improve a Python application's scalability.**
6. **What are common performance bottlenecks in Python web applications, and how would you fix them?**

---

### ✅ **Section 5: Problem-Solving (Coding Challenge)**

1. **Write a Python function that identifies duplicates in a large dataset and removes them while maintaining order.**
2. **Given a list of API endpoints, write an asynchronous Python script to fetch data from all endpoints and combine the results.**
3. **Implement a caching layer in Python using Redis for an API that fetches weather data.**
4. **Write SQL queries for the following:**
    - Find the second highest salary in a table.
    - List all customers who haven’t placed an order in the last 6 months.
5. **(Optional - Debugging Task):** Provide a buggy code snippet and ask the candidate to debug and fix it live.

---

### ✅ **Section 6: Soft Skills & Behavioral Questions**

1. **How do you approach code reviews?** What’s your strategy for giving constructive feedback?
2. **Describe a time when you faced a major roadblock in a project.** How did you overcome it?
3. **Have you mentored junior developers?** Share your approach.
4. **How do you stay updated with new Python libraries, frameworks, and best practices?**
5. **Tell me about a project where you had to collaborate with cross-functional teams.**

---

### 📊 **Evaluation Rubric:**

|Category|Weight|Rating (1-5)|Comments|
|---|---|---|---|
|Core Python Knowledge|20%|||
|System Design|20%|||
|Problem-Solving|20%|||
|Cloud/DevOps (if needed)|15%|||
|Database Expertise|10%|||
|Soft Skills|15%|||


---

### ✅ **Section 7: SQL & Database Design**

#### 📊 **Basic SQL Queries**

1. **Write a query to retrieve the top 5 highest-paid employees from an `employees` table.**
2. **Find the total number of orders placed in the last month from an `orders` table.**
3. **How would you select all customers who haven’t placed any orders?** _(Use `LEFT JOIN` or `NOT EXISTS`)_
4. **Explain the difference between `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL OUTER JOIN`.**
5. **What is the difference between `WHERE` and `HAVING` clauses?**

---

#### 📊 **Intermediate SQL Concepts**

1. **Given a table `sales (id, product_id, quantity, sale_date)`, write a query to find the best-selling product for each month.**
2. **What’s the difference between `RANK()`, `DENSE_RANK()`, and `ROW_NUMBER()`?** Provide examples.
3. **Write a query to find duplicate records in a table and remove them.**
4. **How would you calculate the cumulative sales for each product ordered by sale date?** _(Use window functions)_
5. **Explain the concept of indexing.** When would you avoid using an index?

---

#### 📊 **Advanced SQL & Optimization**

1. **How would you optimize a slow-running query fetching millions of rows?** _(Discuss indexing, query plans, etc.)_
2. **What’s the difference between `UNION` and `UNION ALL`?** When would you use one over the other?
3. **Explain ACID properties in relational databases.** Why are they important?
4. **Design a normalized schema for an e-commerce platform.** _(Cover customers, products, orders, and payments)_
5. **How would you handle schema migrations in a production environment?**

---

#### 📊 **SQL Problem-Solving Challenges**

1. **Given tables `orders(order_id, customer_id, amount, order_date)` and `customers(customer_id, name)`:**
    - Find customers who have placed orders worth more than $5000.
    - List customers who placed orders in **January 2024** but not in **February 2024**.
2. **Write a query to find the second highest salary from an `employees` table.** _(Without using `LIMIT` or `OFFSET`)_
3. **How would you pivot a table in SQL to display monthly sales for each product as columns?** _(Use `CASE WHEN` or `PIVOT` if supported)_
4. **Create a recursive CTE to find the hierarchical structure of employees reporting to a specific manager.**
5. **Given a large transaction table, how would you identify anomalies or suspicious transactions?** _(Discuss window functions or data mining techniques)_

---
