---
layout: post
category: technical
custom_js: mouse_coords
---

# How to integrate FastAPI, Alembic and SQLModel

Alembic is a popular database migration tool in Python used with SQLAlchemy, and it can also be effectively combined with SQLModel for FastAPI applications. SQLModel is built on top of SQLAlchemy and Pydantic, allowing you to define models that can be used both for data validation in FastAPI and as ORM models for database operations.

Here's a general guide on how to use Alembic with SQLModel in a FastAPI project:

### Steps to Integrate Alembic with SQLModel for FastAPI:

#### 1. **Install Dependencies**

First, ensure that you have the necessary dependencies installed:

```bash
pip install fastapi uvicorn alembic sqlmodel
```

You may also need a database driver, depending on the database you're using (e.g., `sqlite`, `psycopg2` for PostgreSQL, or `mysqlclient` for MySQL).

#### 2. **Set up SQLModel**

Create your SQLModel models. These will represent your database tables.

```python
# models.py
from sqlmodel import SQLModel, Field
from typing import Optional

class User(SQLModel, table=True):
    id: Optional[int] = Field(default=None, primary_key=True)
    name: str
    email: str
```

#### 3. **Initialize Alembic**

To start using Alembic, you'll first need to initialize it in your project.

```bash
alembic init alembic
```

This will create a folder named `alembic` in your project directory, along with a `alembic.ini` file that you can configure.

#### 4. **Configure Alembic for SQLModel**

In the `alembic/env.py` file, you need to configure Alembic to work with SQLModel. Update the `env.py` file to import your models and set up the connection to the database.

Replace the content of the `env.py` file with:

```python
from sqlmodel import SQLModel
from sqlalchemy import engine_from_config
from sqlalchemy import pool
from alembic import context
from my_project.models import User  # Import your models here

# this is the Alembic Config object, which provides access to the .ini file values
config = context.config

# Interpret the config file for Python logging.
# This line sets up loggers basically.
import logging.config
fileConfig(config.config_file_name)

# add your model's MetaData object here
target_metadata = SQLModel.metadata

# other values from the config, defined by the needs of env.py, can be acquired:
# my_important_option = config.get_main_option("my_important_option")
# ... etc.


def run_migrations_offline():
    """Run migrations in 'offline' mode.
    """
    url = config.get_main_option("sqlalchemy.url")
    context.configure(
        url=url, target_metadata=target_metadata, literal_binds=True, dialect_opts={"paramstyle": "named"}
    )

    with context.begin_transaction():
        context.run_migrations()


def run_migrations_online():
    """Run migrations in 'online' mode.
    """
    connectable = engine_from_config(
        config.get_section(config.config_ini_section),
        prefix="sqlalchemy.",
        poolclass=pool.NullPool,
    )

    with connectable.connect() as connection:
        context.configure(connection=connection, target_metadata=target_metadata)

        with context.begin_transaction():
            context.run_migrations()


if context.is_offline_mode():
    run_migrations_offline()
else:
    run_migrations_online()
```

#### 5. **Set the Database URL**

In your `alembic.ini` file, update the `sqlalchemy.url` setting with your database URL. For example, if you're using SQLite:

```ini
sqlalchemy.url = sqlite:///./test.db
```

Or if you're using PostgreSQL:

```ini
sqlalchemy.url = postgresql://user:password@localhost/dbname
```

#### 6. **Generate Migrations**

Once everything is set up, you can create an initial migration that captures your current SQLModel models.

```bash
alembic revision --autogenerate -m "Initial migration"
```

This will create a migration script in the `alembic/versions` folder.

#### 7. **Apply the Migration**

To apply the migration and create the tables in your database, run:

```bash
alembic upgrade head
```

This will apply the migration and create the corresponding tables in the database based on your SQLModel definitions.

#### 8. **Integrate with FastAPI**

In your FastAPI application, make sure to create the database tables and set up the connection.

```python
# main.py
from fastapi import FastAPI
from sqlmodel import SQLModel
from sqlmodel import Session, create_engine
from .models import User

app = FastAPI()

DATABASE_URL = "sqlite:///./test.db"
engine = create_engine(DATABASE_URL)

# Create tables (not needed if using Alembic for migrations)
# SQLModel.metadata.create_all(engine)

@app.on_event("startup")
def on_startup():
    SQLModel.metadata.create_all(engine)

@app.get("/users/")
def read_users():
    with Session(engine) as session:
        users = session.query(User).all()
        return users
```

### Summary:
1. Install necessary packages.
2. Set up SQLModel for FastAPI.
3. Initialize Alembic and configure it for SQLModel.
4. Generate migrations and apply them to the database.
5. Integrate Alembic and SQLModel with your FastAPI app for database management.

This setup allows you to manage your SQLModel-based database schemas and apply migrations easily using Alembic.

## If you use PostgreSQL instead of SQLite
If you're using PostgreSQL as the database for your FastAPI project with SQLModel and Alembic, here's how you can adapt the setup to work with PostgreSQL:

### Steps to Set up PostgreSQL with SQLModel, Alembic, and FastAPI:

#### 1. **Install PostgreSQL Driver**

First, install the PostgreSQL driver (`psycopg2`) along with the other dependencies:

```bash
pip install psycopg2-binary
```

#### 2. **Set Up PostgreSQL in Alembic Configuration**

In your `alembic.ini` file, update the `sqlalchemy.url` to use the PostgreSQL URL format:

```ini
sqlalchemy.url = postgresql://user:password@localhost/dbname
```

Replace `user`, `password`, `localhost`, and `dbname` with your actual PostgreSQL credentials and database name.

#### 3. **Adjust the SQLModel and Alembic Setup**

In your `env.py` file (inside the `alembic` directory), ensure it's correctly configured for PostgreSQL. The core of the file should look something like this:

```python
from sqlmodel import SQLModel
from sqlalchemy import engine_from_config
from sqlalchemy import pool
from alembic import context
from my_project.models import User  # Import your models

# Alembic Config object
config = context.config

# Configure logging
import logging.config
fileConfig(config.config_file_name)

# Target metadata (from SQLModel)
target_metadata = SQLModel.metadata

def run_migrations_offline():
    """Run migrations in 'offline' mode."""
    url = config.get_main_option("sqlalchemy.url")
    context.configure(
        url=url, target_metadata=target_metadata, literal_binds=True, dialect_opts={"paramstyle": "named"}
    )

    with context.begin_transaction():
        context.run_migrations()

def run_migrations_online():
    """Run migrations in 'online' mode."""
    connectable = engine_from_config(
        config.get_section(config.config_ini_section),
        prefix="sqlalchemy.",
        poolclass=pool.NullPool,
    )

    with connectable.connect() as connection:
        context.configure(connection=connection, target_metadata=target_metadata)

        with context.begin_transaction():
            context.run_migrations()

if context.is_offline_mode():
    run_migrations_offline()
else:
    run_migrations_online()
```

Make sure your `alembic.ini` points to your PostgreSQL instance by using the `sqlalchemy.url` you configured earlier.

#### 4. **Create Your SQLModel Models**

Here's an example of how your SQLModel models might look:

```python
# models.py
from sqlmodel import SQLModel, Field
from typing import Optional

class User(SQLModel, table=True):
    id: Optional[int] = Field(default=None, primary_key=True)
    name: str
    email: str
```

#### 5. **Generate the Initial Migration**

Once you've set up your models, generate an initial migration:

```bash
alembic revision --autogenerate -m "Initial migration"
```

This will create a migration script in the `alembic/versions` folder that captures the changes in your models.

#### 6. **Apply the Migration to PostgreSQL**

Run the following command to apply the migrations and create the necessary tables in your PostgreSQL database:

```bash
alembic upgrade head
```

This will create the `User` table in the PostgreSQL database, based on your SQLModel model.

#### 7. **Integrate with FastAPI**

In your FastAPI app, make sure to configure the connection to the PostgreSQL database using `sqlmodel` and `psycopg2`. Here's an example `main.py`:

```python
# main.py
from fastapi import FastAPI
from sqlmodel import SQLModel, Session, create_engine
from .models import User

app = FastAPI()

DATABASE_URL = "postgresql://user:password@localhost/dbname"
engine = create_engine(DATABASE_URL, echo=True)

# Optionally, create tables (useful for initial development if not using Alembic)
# SQLModel.metadata.create_all(engine)

@app.on_event("startup")
def on_startup():
    SQLModel.metadata.create_all(engine)  # In production, you might skip this when using Alembic

@app.get("/users/")
def read_users():
    with Session(engine) as session:
        users = session.query(User).all()
        return users
```

### Key Points:

1. **PostgreSQL URL**: Ensure your `alembic.ini` file contains the correct PostgreSQL URL.
2. **Psycopg2**: Make sure the PostgreSQL driver (`psycopg2-binary`) is installed.
3. **Database Configuration**: Configure your FastAPI application to use PostgreSQL via the `create_engine` method.
4. **Alembic Migrations**: Use `alembic` to manage database migrations effectively. You can generate and apply migrations using `alembic revision --autogenerate` and `alembic upgrade head`.

By following these steps, you'll be able to integrate Alembic with SQLModel and PostgreSQL in your FastAPI project.

## You should use a .env file to store you DB credentials

To securely store your database credentials in a `.env` file and load them into your FastAPI project, you can use the **python-dotenv** library. Here's how to set it up:

### Steps to Save DB Credentials in `.env` File:

#### 1. **Install `python-dotenv`**

First, install the `python-dotenv` package:

```bash
pip install python-dotenv
```

#### 2. **Create a `.env` File**

Create a `.env` file in the root directory of your project. In this file, you can store your database credentials:

```ini
# .env
DATABASE_URL=postgresql://user:password@localhost/dbname
```

Make sure to replace `user`, `password`, `localhost`, and `dbname` with your actual PostgreSQL credentials and database name.

#### 3. **Update FastAPI to Load Environment Variables**

Now, update your FastAPI app to load the environment variables using `python-dotenv`. In your `main.py` or wherever you're setting up the database connection, load the `.env` file like this:

```python
# main.py
import os
from dotenv import load_dotenv
from fastapi import FastAPI
from sqlmodel import SQLModel, Session, create_engine

# Load environment variables from .env file
load_dotenv()

# Get the database URL from the environment variable
DATABASE_URL = os.getenv("DATABASE_URL")

# Create the database engine
engine = create_engine(DATABASE_URL, echo=True)

app = FastAPI()

# Optionally create tables (useful for development; can be skipped in production)
@app.on_event("startup")
def on_startup():
    SQLModel.metadata.create_all(engine)

@app.get("/users/")
def read_users():
    with Session(engine) as session:
        users = session.query(User).all()
        return users
```

#### 4. **Update Alembic to Use the `.env` File**

You’ll also need to modify your Alembic configuration so that it reads the database credentials from the `.env` file.

In your `alembic.ini`, keep the `sqlalchemy.url` like this:

```ini
# alembic.ini
sqlalchemy.url = postgresql://dummy_url
```

Then, in your `alembic/env.py`, load the `.env` file and update the `sqlalchemy.url` dynamically:

```python
# alembic/env.py
import os
from dotenv import load_dotenv
from sqlalchemy import engine_from_config
from sqlalchemy import pool
from alembic import context
from sqlmodel import SQLModel
from my_project.models import User  # Import your models

# Load environment variables from the .env file
load_dotenv()

# this is the Alembic Config object, which provides access to the .ini file values
config = context.config

# Override the sqlalchemy.url from the .env file
config.set_main_option("sqlalchemy.url", os.getenv("DATABASE_URL"))

# Setup logging
import logging.config
fileConfig(config.config_file_name)

# Target metadata
target_metadata = SQLModel.metadata

def run_migrations_offline():
    """Run migrations in 'offline' mode."""
    url = config.get_main_option("sqlalchemy.url")
    context.configure(
        url=url, target_metadata=target_metadata, literal_binds=True, dialect_opts={"paramstyle": "named"}
    )

    with context.begin_transaction():
        context.run_migrations()

def run_migrations_online():
    """Run migrations in 'online' mode."""
    connectable = engine_from_config(
        config.get_section(config.config_ini_section),
        prefix="sqlalchemy.",
        poolclass=pool.NullPool,
    )

    with connectable.connect() as connection:
        context.configure(connection=connection, target_metadata=target_metadata)

        with context.begin_transaction():
            context.run_migrations()

if context.is_offline_mode():
    run_migrations_offline()
else:
    run_migrations_online()
```

#### 5. **Add `.env` to `.gitignore`**

Since `.env` files often contain sensitive information (like your database credentials), make sure to add it to your `.gitignore` file so that it's not accidentally committed to version control.

```bash
# .gitignore
.env
```

### Summary:

1. **Install `python-dotenv`.**
2. **Create a `.env` file** to store your database credentials.
3. **Use `load_dotenv()`** to load environment variables into your FastAPI app and Alembic configuration.
4. **Modify Alembic’s `env.py`** to read the `DATABASE_URL` from the `.env` file.
5. **Add `.env` to `.gitignore`** to avoid committing sensitive credentials.

By doing this, you can securely manage your database credentials without hardcoding them into your code.

## The proper code with Error Handling 
Adding error handling to your FastAPI project, especially around database connection and querying, is crucial for building a robust application. Here's how you can improve the existing code by adding error handling:

### Key Error Handling Considerations:
1. **Database Connection Errors**: Handle errors that may occur when connecting to the database.
2. **Query Execution Errors**: Handle errors that may occur when querying the database.
3. **Environment Variable Errors**: Ensure that critical environment variables are available.
4. **Return Meaningful HTTP Responses**: Use FastAPI’s `HTTPException` to provide meaningful error messages to the client.

Here’s an updated version of the code with error handling:

### Updated Code with Error Handling:

```python
import os
from dotenv import load_dotenv
from fastapi import FastAPI, HTTPException, status
from sqlmodel import SQLModel, Session, create_engine
from sqlalchemy.exc import SQLAlchemyError, OperationalError

# Load environment variables from .env file
load_dotenv()

# Get the database URL from environment variables
DATABASE_URL = os.getenv("DATABASE_URL")

if not DATABASE_URL:
    raise RuntimeError("DATABASE_URL is not set in the .env file")

# Create the database engine
try:
    engine = create_engine(DATABASE_URL, echo=True)
except SQLAlchemyError as e:
    raise RuntimeError(f"Error creating database engine: {e}")

app = FastAPI()

# Define models (example)
class User(SQLModel, table=True):
    id: Optional[int] = Field(default=None, primary_key=True)
    name: str
    email: str

# On startup, try to create tables, log any errors
@app.on_event("startup")
async def on_startup():
    try:
        SQLModel.metadata.create_all(engine)
    except OperationalError as e:
        print(f"Error during table creation: {e}")
        raise RuntimeError("Could not connect to the database during startup.")

# Endpoint to fetch users with error handling
@app.get("/users/", response_model=List[User])
def read_users():
    try:
        with Session(engine) as session:
            users = session.query(User).all()
            if not users:
                raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="No users found")
            return users
    except OperationalError as e:
        raise HTTPException(status_code=status.HTTP_500_INTERNAL_SERVER_ERROR, detail=f"Database connection error: {e}")
    except SQLAlchemyError as e:
        raise HTTPException(status_code=status.HTTP_500_INTERNAL_SERVER_ERROR, detail=f"Database query error: {e}")
```

### Key Points of Error Handling:

1. **Database Connection Errors**:
    - If `DATABASE_URL` is missing from the environment variables, a `RuntimeError` is raised.
    - When creating the database engine, any `SQLAlchemyError` (e.g., incorrect credentials or database URL) is caught, and a meaningful error is raised.
  
2. **Table Creation on Startup**:
    - During startup, when tables are created, any `OperationalError` (e.g., database connection issues) is logged, and a `RuntimeError` is raised.
  
3. **Query Execution Errors**:
    - When querying the database, if no users are found, a `404` error is returned using FastAPI’s `HTTPException`.
    - If there is any error with database queries (e.g., SQL syntax error or invalid connection), the `SQLAlchemyError` is caught, and a `500 Internal Server Error` is raised.

4. **Return Meaningful HTTP Responses**:
    - When something goes wrong (e.g., `OperationalError`, `SQLAlchemyError`), meaningful `HTTPException` errors are raised so that the client gets clear feedback about what went wrong.

### Breakdown of Error Scenarios:

1. **Missing Database URL**:
    - If `DATABASE_URL` is not set, the application will not start and will raise a `RuntimeError`.

2. **Database Connection Issues**:
    - If the application cannot connect to the database (e.g., during startup), it logs the error and stops the process.
    - If the database connection fails during query execution, it returns a `500 Internal Server Error` with details.

3. **No Data Found**:
    - If no users are found, a `404 Not Found` response is returned to the client.

### Conclusion:
This setup ensures that your FastAPI application handles critical errors gracefully, provides meaningful error messages, and doesn’t crash unexpectedly. You can expand on this error handling to catch more specific exceptions and customize error messages for better client communication.