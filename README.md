# 🧠 Natural Language to SQL with Vanna, Ollama, and SQLite

This project allows you to ask questions in plain English and automatically convert them into SQL queries using a local AI model. The queries run on a lightweight SQLite database, and results are displayed in a simple table format.

---

## ✨ What This Project Does

* Translates natural language (like: *"Who are VIP customers from Egypt?"*) into SQL
* Uses a local language model through **Ollama** (e.g. `mistral`)
* Doesn't require you to define the database structure in SQL (just describe it in plain English)
* Works completely offline

---

## 🗂 How It Works

1. **Model Setup**:
   The project uses `Ollama` to run a local AI model. By default, it uses `mistral`, but you can change the model to any other supported by Ollama (e.g., `llama3`, `gemma`, etc.).

   To switch models, simply modify the configuration:

   ```python
   vn = MyVanna(config={'model': 'llama3'})
   ```

   And make sure to pull and run the new model:

   ```bash
   ollama run llama3
   ```

2. **Database Creation and Context**:
   A local SQLite database named `test.db` is created with a table called `customers`. It contains fields such as `id`, `name`, `age`, `country`, and `total_spent`. The table is populated with sample data to test the system.

   Instead of passing traditional SQL schema (DDL), we describe the structure of this database in plain English. For example:

   > "We have a table called 'customers' with columns for name, age, country, and total spending. VIP customers are defined as those who spent more than 10,000 or are over 50 years old."

   This natural language context helps the model understand how to form SQL queries without any formal training step.
How the Database is Connected:

The connection to the SQLite database is handled using the sqlite3 library. 

A connection object is created using sqlite3.connect("test.db")

A cursor is created to execute SQL commands

The table and data are created using SQL commands in Python

Once done, the connection is committed and closed

If you're using Vanna’s SQLite connector, you can register the database like this:

from vanna.sqlite import SQLite_Connector

vn.connect_to_sqlite("test.db")

3. **Modifications to Vanna Library**:
   By default, Vanna expects `.train()` to be used for passing documentation. In this project, we modified the internal `generate_sql()` method inside Vanna’s `base.py` file to accept a new parameter called `extra_doc`. This allows passing schema context dynamically, without requiring persistent training. It makes the project more flexible and stateless — perfect for notebooks or APIs.

   The `extra_doc` is merged into the prompt sent to the model, improving the relevance and accuracy of the generated SQL.

4. **Asking Questions**:
   Once the database and schema context are ready, you can ask questions like:

   > "Show me the VIP customers from Egypt"

   The modified model will respond with an appropriate SQL query, which is then executed on the local database.

---

## 🔧 Setup Steps

* Install [Ollama](https://ollama.com) and run the desired model (e.g. `mistral`, `llama3`, `gemma`)

* Install Python packages using:

  ```bash
  pip install pandas
  pip install 'vanna[chromadb,ollama,sqlite]'
  ```

* Run the notebook and follow the steps to:

  * Create the database
  * Define the schema context
  * Ask questions and get results
