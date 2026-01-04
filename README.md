# Airflow ETL Pipeline with PostgreSQL and NASA API on Astro Cloud

A comprehensive guide to building an automated ETL pipeline using Apache Airflow, PostgreSQL, and the NASA API on Astronomer's managed platform.

---

## Table of Contents

1. [What is Astronomer (Astro)?](#what-is-astronomer-astro)
2. [Prerequisites](#prerequisites)
3. [Installation Guide](#installation-guide)
4. [Building the ETL Pipeline](#building-the-etl-pipeline)
5. [Database Setup](#database-setup)
6. [Airflow Configuration](#airflow-configuration)
7. [Running Your Pipeline](#running-your-pipeline)
8. [Troubleshooting](#troubleshooting)

---

## What is Astronomer (Astro)?

**Astronomer (Astro)** is a managed platform for Apache Airflow that simplifies deployment, scaling, and management of data workflows.

**Key Features:**
- Managed infrastructure with simplified Airflow deployment
- Docker-based architecture for containerized workflows
- Automatic scaling and monitoring capabilities

**Official Website:** [astronomer.io](https://www.astronomer.io/)

---

## Prerequisites

### System Requirements
- **Operating System:** Windows 10 or later
- **RAM:** Minimum 8GB recommended
- **Disk Space:** At least 10GB free

### Required Software
- **Docker Desktop for Windows**
  - Download: [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)
  - Required for running Airflow containers locally

---

## Installation Guide

### Step 1: Install Docker Desktop

1. Download Docker Desktop from the official website
2. Run the installer and follow the setup wizard
3. Restart your computer when prompted
4. Launch Docker Desktop and verify it's running (look for the whale icon in system tray)

---

### Step 2: Verify Windows Package Manager (winget)

Open Command Prompt or PowerShell and check if winget is installed:

```cmd
winget --version
```

**Expected Output:** Version number like `v1.7.11261`

![winget version check](https://github.com/user-attachments/assets/b6fb4f68-6843-4de9-939c-00b563662ef2)

- ✅ **If you see a version number** → Skip to [Step 5](#step-5-install-astro-cli)
- ❌ **If you see "winget is not recognized"** → Continue to Step 3

---

### Step 3: Install Windows Package Manager

If winget is not available, follow these substeps carefully.

#### 3.1 Install App Installer

1. Visit [Microsoft Store - App Installer](https://apps.microsoft.com/detail/9nblggh4nns1?hl=en-US&gl=IN)
2. Click "Get" or "Install"
3. Wait for installation to complete

#### 3.2 Verify Installation Directory

After installation, Windows creates this folder:

```
C:\Users\<your-username>\AppData\Local\Microsoft\WindowsApps
```

![WindowsApps directory](https://github.com/user-attachments/assets/965d0030-a438-4b69-ae6d-1969cb206a99)

This directory contains `winget.exe`.

#### 3.3 Test winget Again

```cmd
winget --version
```

- ✅ **If successful** → Skip to [Step 5](#step-5-install-astro-cli)
- ❌ **If still failing** → Continue to Step 3.4

---

#### 3.4 Add winget to Environment Variables (PATH)

**⚠️ CRITICAL STEP** - This is the most common source of installation issues.

**Method 1: Using Windows Search**
1. Press `Win` key and search for "environment variables"
2. Select "Edit the system environment variables"

**Method 2: Using System Properties**
1. Press `Win + X` and select "System"
2. Click "Advanced system settings"
3. Click "Environment Variables" button

![Environment Variables dialog](https://github.com/user-attachments/assets/316da660-2a8d-4d59-9c48-775de6e63ce6)

**Edit PATH Variable:**
1. In the "User variables" section, select `Path`
2. Click "Edit"
3. Click "New"
4. Add this path (replace `<your-username>` with your actual Windows username):
   ```
   C:\Users\<your-username>\AppData\Local\Microsoft\WindowsApps
   ```

![Editing PATH variable](https://github.com/user-attachments/assets/ae6481e3-2338-4814-b600-db210a2e307b)

5. Click "OK" on all dialogs to save

---

#### 3.5 Restart Your Terminal

**⚠️ VERY IMPORTANT:** Environment variable changes require a terminal restart.

**Choose one option:**
- **Option 1:** Close all Command Prompt/PowerShell/VS Code terminal windows, then reopen
- **Option 2:** Restart your computer (recommended for complete refresh)

**Why this matters:** Windows only reads environment variables when a process starts. Your existing terminal has already loaded the old PATH and won't recognize the new entry until restarted.

---

### Step 4: Final Verification

After restarting your terminal:

```cmd
winget --version
```

**Expected Output:** Version number (e.g., `v1.7.11261`)

- ✅ **Success** → Proceed to Step 5
- ❌ **Still failing** → Ensure all terminal windows are closed, then repeat Step 3.5

---

### Step 5: Install Astro CLI

With winget verified and working, install the Astronomer Astro CLI:

```cmd
winget install -e --id Astronomer.Astro
```

**Command Breakdown:**
- `winget install` - Package manager install command
- `-e` - Exact match flag (ensures precise package identification)
- `--id Astronomer.Astro` - Unique package identifier

**Installation typically takes 2-5 minutes.**

---

### Step 6: Verify Astro CLI Installation

**Important:** Restart your terminal after installation to update the PATH.

Check the Astro version:

```cmd
astro version
```

**Expected Output:** Version information displaying Astro CLI and Git versions

![Astro version output](https://github.com/user-attachments/assets/e2aca339-568f-40a2-a0af-9eb55cb36de4)

- ✅ **Success:** You're ready to use Astro CLI
- ❌ **Command not found:** Restart terminal again and ensure Docker Desktop is running

---

### Step 7: Initialize Your First Airflow Project

Ensure Docker Desktop is running, then:

1. **Create a project directory:**
   ```cmd
   mkdir my-airflow-project
   cd my-airflow-project
   ```

2. **Initialize Astro project:**
   ```cmd
   astro dev init
   ```
   
   This creates the necessary project structure with folders for DAGs, plugins, and configuration files.

   ![Astro dev init](https://github.com/user-attachments/assets/40bc8d8e-eb87-479a-bcc9-0aff4fa6b5f1)

3. **Start Airflow locally:**
   ```cmd
   astro dev start
   ```
   
   This command:
   - Pulls necessary Docker images
   - Starts Airflow webserver, scheduler, and database containers
   - Takes 2-5 minutes on first run

   ![Astro dev start](https://github.com/user-attachments/assets/8348ef0a-21bf-4a1e-b7a7-ada2f6cc30c1)

4. **Access Airflow UI:**
   - Navigate to `http://localhost:8080` in your browser
   - Default credentials: `admin` / `admin`
   - **Note:** Empty DAG list is normal on first launch

   ![Airflow UI initial view](https://github.com/user-attachments/assets/ced2040c-dc28-4f67-9bea-3a767f30ee25)

---

## Building the ETL Pipeline

### Overview

We'll build an ETL (Extract, Transform, Load) pipeline that:
1. **Extracts** daily astronomy data from NASA's APOD API
2. **Transforms** the JSON response into structured data
3. **Loads** the data into a PostgreSQL database

---

### Step 1: Get NASA API Key

1. Visit [api.nasa.gov](https://api.nasa.gov/)
2. Fill out the API key request form:
   - First Name (required)
   - Last Name (required)
   - Email (required)
   - Usage description (optional)
3. Receive your API key via email (usually instant)

<img width="889" alt="NASA API Key Request" src="https://github.com/user-attachments/assets/d43b1172-4aed-48a8-a99c-361e0d24ce2f" />

---

### Step 2: Create the DAG File

Navigate to your project's `dags` folder and create `nasa_etl.py`:

<img width="1443" alt="DAG File Location" src="https://github.com/user-attachments/assets/23878d2e-a18e-4dfc-91df-6aadd6578962" />

Complete DAG Code

```python
from airflow import DAG
from airflow.providers.http.operators.http import HttpOperator
from airflow.decorators import task
from airflow.providers.postgres.hooks.postgres import PostgresHook
from datetime import datetime, timedelta
from airflow.providers.microsoft.mssql.hooks.mssql import MsSqlHook

with DAG(
    dag_id="nasa_board_postgres",
    start_date=datetime.now() - timedelta(days=1),
    schedule="@daily",
    catchup=False
) as dag:

    @task
    def create_table():
        postgres_hook = PostgresHook(postgres_conn_id="my_postgres_connection")
        create_table_query = """
            CREATE TABLE IF NOT EXISTS apod_data (
                id SERIAL PRIMARY KEY,
                title VARCHAR(255),
                explanation TEXT,
                url TEXT,
                date DATE,
                media_type VARCHAR(50)
            );
        """
        postgres_hook.run(create_table_query)

    extract_apod = HttpOperator(
        task_id="extract_apod",
        http_conn_id="nasa_api",
        endpoint="planetary/apod",
        method="GET",
        data={"api_key": "{{ conn.nasa_api.extra_dejson.api_key }}"},
        response_filter=lambda r: r.json(),
        log_response=True,
    )

    @task
    def transform_operator_data(response):
        upload_data = {
            'title': response.get('title', ''),
            'explanation': response.get('explanation', ''),
            'url': response.get('url', ''),
            'date': response.get('date', ''),
            'media_type': response.get('media_type', '')
        }
        return upload_data

    @task
    def load_data_to_postgres(apod_data):
        postgres_hook = PostgresHook(postgres_conn_id='my_postgres_connection')
        insert_query = """
            INSERT INTO apod_data (title, explanation, url, date, media_type)
            VALUES (%s, %s, %s, %s, %s)
        """
        params = (
            apod_data['title'],
            apod_data['explanation'],
            apod_data['url'],
            apod_data['date'],
            apod_data['media_type']
        )
        postgres_hook.run(insert_query, parameters=params)

    # DAG Dependencies
    create_table() >> extract_apod
    api_response = extract_apod.output
    transformed_data = transform_operator_data(api_response)
    load_data_to_postgres(transformed_data)
```

---

## Import Breakdown

```python
from airflow import DAG
from airflow.providers.http.operators.http import HttpOperator
from airflow.decorators import task
from airflow.providers.postgres.hooks.postgres import PostgresHook
from datetime import datetime, timedelta
from airflow.providers.microsoft.mssql.hooks.mssql import MsSqlHook
```

- **`DAG`** → Defines the workflow (Directed Acyclic Graph)  
- **`HttpOperator`** → Makes HTTP requests to external APIs  
- **`@task`** → Decorator to define Python tasks in Airflow  
- **`PostgresHook`** → Simplifies PostgreSQL connections with retries  
- **`datetime, timedelta`** → For scheduling and date operations  
- **`MsSqlHook`** → (Optional) For connecting to Microsoft SQL Server  

---

### Step 3: Define the DAG

```python
with DAG(
    dag_id="nasa_apod_postgres",
    start_date=datetime.now() - timedelta(days=1),
    schedule="@daily",
    catchup=False
) as dag:
```

**Parameters:**
- `dag_id` - Unique identifier for this pipeline
- `start_date` - When the DAG should begin (set to yesterday)
- `schedule="@daily"` - Runs automatically once per day
- `catchup=False` - Only runs for future dates, skips historical backfill

---

### Step 4: Create Database Table

```python
    @task
    def create_table():
        """Creates the PostgreSQL table if it doesn't exist"""
        postgres_hook = PostgresHook(postgres_conn_id="my_postgres_connection")
        
        create_table_query = """
            CREATE TABLE IF NOT EXISTS apod_data (
                id SERIAL PRIMARY KEY,
                title VARCHAR(255),
                explanation TEXT,
                url TEXT,
                date DATE,
                media_type VARCHAR(50)
            );
        """
        
        postgres_hook.run(create_table_query)
```

**What's Happening:**
- `@task` decorator converts the function into an Airflow task
- `SERIAL PRIMARY KEY` auto-increments the ID for each record
- `IF NOT EXISTS` ensures idempotency (safe to run multiple times)

---

### Step 5: Extract Data from NASA API

```python
    extract_apod = HttpOperator(
        task_id="extract_apod",
        http_conn_id="nasa_api",
        endpoint="planetary/apod",
        method="GET",
        data={"api_key": "{{ conn.nasa_api.extra_dejson.api_key }}"},
        response_filter=lambda r: r.json(),
        log_response=True,
    )
```

**Parameters:**
- `task_id` - Name of this task in the DAG
- `http_conn_id` - Connection ID configured in Airflow UI
- `endpoint` - API endpoint (Astronomy Picture of the Day)
- `method` - HTTP GET request
- `data` - Passes API key from Airflow connection using Jinja templating
- `response_filter` - Converts HTTP response to JSON
- `log_response` - Logs API response for debugging

---

### Step 6: Transform the Data

```python
    @task
    def transform_apod_data(response):
        """Extracts relevant fields from API response"""
        transformed_data = {
            'title': response.get('title', ''),
            'explanation': response.get('explanation', ''),
            'url': response.get('url', ''),
            'date': response.get('date', ''),
            'media_type': response.get('media_type', '')
        }
        return transformed_data
```

**Purpose:**
- Extracts specific fields from the API JSON response
- Formats data for database insertion
- `.get()` method provides default empty string if field is missing (defensive programming)

---

### Step 7: Load Data into PostgreSQL

```python
    @task
    def load_data_to_postgres(apod_data):
        """Inserts transformed data into PostgreSQL"""
        postgres_hook = PostgresHook(postgres_conn_id='my_postgres_connection')
        
        insert_query = """
            INSERT INTO apod_data (title, explanation, url, date, media_type)
            VALUES (%s, %s, %s, %s, %s)
        """
        
        params = (
            apod_data['title'],
            apod_data['explanation'],
            apod_data['url'],
            apod_data['date'],
            apod_data['media_type']
        )
        
        postgres_hook.run(insert_query, parameters=params)
```

**What's Happening:**
- Uses parameterized query (`%s` placeholders) to prevent SQL injection
- `params` tuple provides values for the query in order
- PostgresHook handles connection management automatically

---

### Step 8: Define Task Dependencies

```python
    # Set up task execution order
    create_table() >> extract_apod
    
    # Get API response and pass it through transformation
    api_response = extract_apod.output
    transformed_data = transform_apod_data(api_response)
    
    # Load the transformed data
    load_data_to_postgres(transformed_data)
```

**Execution Flow:**
1. Create table (if needed)
2. Extract data from NASA API
3. Transform the extracted data
4. Load transformed data into PostgreSQL

**The `>>` operator defines task dependencies** (read as "then").

---

## Database Setup

### Step 1: Create Docker Compose File

Create `docker-compose.yml` in your project root directory:

<img width="886" alt="Docker Compose File" src="https://github.com/user-attachments/assets/09dd0490-db73-4ece-80e9-d27cbf8b0938" />

Complete Code:
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:13
    container_name: postgres_db
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: airflow_db
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - airflow_network

volumes:
  postgres_data:

networks:
  airflow_network:
    driver: bridge
```

**Configuration Explained:**

**Service Definition:**
- `image: postgres:13` - Uses PostgreSQL version 13 Docker image
- `container_name` - Name for easy reference

**Environment Variables:**
- `POSTGRES_USER` - Database superuser username
- `POSTGRES_PASSWORD` - Database password
- `POSTGRES_DB` - Initial database name

**Port Mapping:**
- `"5432:5432"` - Format is `"host_port:container_port"`
- Maps container's PostgreSQL port to your local machine

**Volumes:**
- `postgres_data:/var/lib/postgresql/data` - Persists database data
- Data survives container restarts and removals

**Networks:**
- `airflow_network` - Bridge network enabling communication between containers
- Allows Airflow and PostgreSQL containers to discover each other by service name

---

### Step 2: Start PostgreSQL Container

```cmd
docker-compose up -d
```

**What This Does:**
- `-d` flag runs containers in detached mode (background)
- Pulls PostgreSQL image if not already downloaded
- Creates and starts the container
- Sets up the network and volumes

**Verify Container is Running:**

```cmd
docker ps
```

You should see `postgres_db` in the list of running containers.

---

## Airflow Configuration

### Setting Up Connections in Airflow UI

Connections allow Airflow to securely communicate with external systems.

---

### PostgreSQL Connection

1. **Access Admin Panel:**
   - Open Airflow UI at `http://localhost:8080`
   - Click **Admin** in the left sidebar
   - Select **Connections**
   - Click the **+** button

   ![Admin Connections menu](https://github.com/user-attachments/assets/14000a4f-c4c9-4b2a-87ec-47c62f962d7b)

2. **Configure Connection:**
   - **Connection Id:** `my_postgres_connection`
   - **Connection Type:** `Postgres`
   - **Host:** `postgres_db` (the container name from docker-compose.yml)
   
   **Finding the Container Name:**
   - Open Docker Desktop
   - Find your PostgreSQL container
   - Copy the container name/hostname

   ![Docker container name](https://github.com/user-attachments/assets/d878ecdf-5c2e-4272-baf8-f630479017ec)

   - **Database:** `airflow_db`
   - **Login:** `postgres`
   - **Password:** `postgres`
   - **Port:** `5432`

   ![PostgreSQL connection configuration](https://github.com/user-attachments/assets/26958ed7-cfba-45cc-9806-e904d11da2c6)

3. **Test and Save:**
   - Click "Test" to verify connectivity
   - Click "Save" when successful

**Why use container name as host?** When containers are on the same Docker network, they can reference each other by service name. Docker's internal DNS resolves `postgres_db` to the correct container IP.

---

### NASA API Connection

1. **Add New Connection:**
   - Click the **+** button in Connections
   - **Connection Id:** `nasa_api`
   - **Connection Type:** `HTTP`

2. **Configure API Details:**
   - **Host:** `https://api.nasa.gov`
   - **Extra:** Add your API key in JSON format:
     ```json
     {
       "api_key": "YOUR_NASA_API_KEY_HERE"
     }
     ```

   ![HTTP API connection](https://github.com/user-attachments/assets/aec157f4-39df-433e-99fa-f9bf842dbd18)

3. **Save the connection**

**Security Note:** The Extra field stores configuration as JSON, which Airflow encrypts using Fernet keys.

---

## Running Your Pipeline

### Step 1: Deploy the DAG

1. Save your `nasa_etl.py` file in the `dags` folder
2. Airflow automatically detects new DAG files (refresh interval: 30 seconds)
3. Check the Airflow UI - your DAG should appear in the list

---

### Step 2: Trigger the DAG

**Manual Trigger:**
1. Find `nasa_apod_postgres` in the DAG list
2. Toggle the DAG to "On" (switch on the left)
3. Click the "Play" button to trigger manually

**The DAG will also run automatically daily based on the schedule.**

---

### Step 3: Monitor Execution

1. Click on the DAG name to view details
2. Click "Graph" view to see task dependencies visually
3. Watch tasks turn from white (queued) → light green (running) → dark green (success)

![Successful DAG run](https://github.com/user-attachments/assets/dfa81deb-6514-4e7c-b5b3-c1ea512c5525)

**Task Colors:**
- White/Gray: Queued or scheduled
- Light Green: Running
- Dark Green: Success
- Red: Failed
- Orange: Upstream failed

---

### Step 4: View Task Outputs

**Checking Logs:**
1. Click on any task box (e.g., `extract_apod`)
2. Select "Logs" to view execution details
3. Logs show API responses, SQL queries, and any errors

![Task details](https://github.com/user-attachments/assets/4fbef749-c66e-4a63-8ec4-5001863c8adf)

**Viewing XCom Data:**
1. Click on the `extract_apod` task
2. Select "XCom" tab
3. View the JSON data passed between tasks

![XCom data view](https://github.com/user-attachments/assets/7610808b-c807-4ee4-bbe9-4aa57221a392)

XCom (cross-communication) allows tasks to share data within a DAG run.

---

### Step 5: Verify Database Records

**Using DBeaver (Recommended):**

1. Download and install [DBeaver](https://dbeaver.io/download/)
2. Create a new PostgreSQL connection:
   - **Host:** `localhost`
   - **Port:** `5432`
   - **Database:** `airflow_db`
   - **Username:** `postgres`
   - **Password:** `postgres`
3. Connect and navigate to `public.apod_data` table
4. View your NASA APOD records

**Using Command Line:**

```cmd
docker exec -it postgres_db psql -U postgres -d airflow_db
```

Then query the table:

```sql
SELECT * FROM apod_data ORDER BY date DESC LIMIT 5;
```

