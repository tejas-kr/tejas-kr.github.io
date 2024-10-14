---
layout: post
category: technical
custom_js: mouse_coords
---

# How to store passwords in DB [Python]

Storing passwords securely is critical to protect user data. Here’s a secure way to store passwords in a database:

---

## **Best Practices for Password Storage:**

1. **Use Hashing, Not Encryption:**  
   - **Encryption** can be reversed if the key is compromised.
   - **Hashing** is a one-way function that turns input into a fixed-size string. It’s irreversible.

2. **Use Salted Hashes:**  
   - A **salt** is a unique, random value added to the password before hashing to prevent attacks like **rainbow table attacks**.
   - Each user’s password should have its own unique salt.

3. **Use Strong Hashing Algorithms:**  
   - Use algorithms like **bcrypt**, **argon2**, or **PBKDF2**. These algorithms are slow by design, making brute-force attacks less feasible.

---

## **Example in Python using `bcrypt`**

### 1. Installation
```bash
pip install bcrypt
```

### 2. Code for Hashing and Storing Password

```python
import bcrypt

# Hash the password
def hash_password(plain_password: str) -> bytes:
    salt = bcrypt.gensalt()  # Generates a salt
    hashed = bcrypt.hashpw(plain_password.encode(), salt)
    return hashed

# Verify password during login
def verify_password(plain_password: str, hashed_password: bytes) -> bool:
    return bcrypt.checkpw(plain_password.encode(), hashed_password)

# Example usage
plain_password = "SecurePassword123"
hashed_password = hash_password(plain_password)

print(f"Hashed Password: {hashed_password}")

# Verify during login
is_valid = verify_password("SecurePassword123", hashed_password)
print(f"Password is valid: {is_valid}")
```

### 3. Storing Hashed Password in the Database  
- Store the `hashed_password` as a **binary string (BLOB)** or **base64-encoded string** in your database.
  
```sql
-- Example table creation
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    password_hash BYTEA NOT NULL -- or VARCHAR if using base64
);
```

- When a user registers, **insert** the hashed password into the database:
  
```sql
INSERT INTO users (username, password_hash) VALUES ('john_doe', ?);
```

---

## **Why `bcrypt` or `argon2`?**
- **Slow hashing** algorithms like bcrypt and argon2 prevent brute force attacks effectively.
- These algorithms automatically generate salts internally and are resistant to timing attacks.

---

## **Additional Security Recommendations:**
1. **Use HTTPS**: Ensure data in transit is encrypted.
2. **Password Length Policy**: Enforce strong passwords (e.g., at least 12 characters).
3. **Rate Limiting**: Use rate limiting to prevent brute-force attacks.
4. **Account Lockouts**: Lock accounts after multiple failed attempts.

Following these steps ensures that even if the database is compromised, the passwords remain secure.