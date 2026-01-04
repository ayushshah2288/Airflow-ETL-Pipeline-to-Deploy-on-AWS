# Airflow ETL Pipeline with PostgreSQL and API Integration on Astro Cloud

## What is Astronomer (Astro)?

**Astronomer (Astro)** is a managed platform for Apache Airflow that simplifies the deployment, scaling, and management of Airflow workflows. Key features include:

- **Managed Infrastructure**: Simplified Airflow deployment and scaling
- **Docker-Based Architecture**: Runs Airflow within Docker containers

**Official Website**: [https://www.astronomer.io/](https://www.astronomer.io/)

---

## Prerequisites

### System Requirements
- **Operating System**: Windows 10 or later

### Required Software
- **Docker Desktop for Windows**
  - Download: [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
  - Required for running Airflow containers

---

## Installation Steps

### Step 1: Install Docker Desktop

1. Download Docker Desktop from the link above
2. Run the installer and follow the installation wizard
3. Restart your computer if prompted
4. Launch Docker Desktop and ensure it's running

### Step 2: Verify Windows Package Manager (winget)

1. Open Command Prompt (CMD) or PowerShell
2. Check if winget is installed:
   ```cmd
   winget --version
   ```

**Expected Result**: A version number (e.g., `v1.7.11261`)

![winget version check](https://github.com/user-attachments/assets/b6fb4f68-6843-4de9-939c-00b563662ef2)

- ✅ **If you see a version number**, proceed to **Step 5**
- ❌ **If you see an error** ("winget is not recognized"), continue to **Step 3**

### Step 3: Install Windows Package Manager (If Required)

If winget is not recognized, follow these substeps:

#### 3.1 Install App Installer from Microsoft Store

1. Visit the Microsoft Store: [App Installer](https://apps.microsoft.com/detail/9nblggh4nns1?hl=en-US&gl=IN)
2. Click "Get" or "Install"
3. Wait for installation to complete

#### 3.2 Verify Installation Directory

After installation, Windows creates the following folder:
```
C:\Users\<your-username>\AppData\Local\Microsoft\WindowsApps
```

![WindowsApps directory](https://github.com/user-attachments/assets/965d0030-a438-4b69-ae6d-1969cb206a99)

This directory contains `winget.exe`.

#### 3.3 Test winget Again

Open Command Prompt and run:
```cmd
winget --version
```

- ✅ **If you see a version number**, proceed to **Step 5**
- ❌ **If you still see an error**, continue to **Step 3.4**

#### 3.4 Add to Environment Variables (PATH)

**⚠️ CRITICAL STEP - Most Common Source of Issues**

1. **Open Environment Variables**:
   - Press `Win + X` and select "System"
   - Click "Advanced system settings"
   - Click "Environment Variables"
   - *Or search for "environment" in Windows search and select "Edit environment variables"*

   ![Environment Variables dialog](https://github.com/user-attachments/assets/316da660-2a8d-4d59-9c48-775de6e63ce6)

2. **Edit User PATH Variable**:
   - In the "User variables" section, select `Path`
   - Click "Edit"
   - Click "New"
   - Add the following path:
     ```
     C:\Users\<your-username>\AppData\Local\Microsoft\WindowsApps
     ```
     *(Replace `<your-username>` with your actual Windows username)*

   ![Editing PATH variable](https://github.com/user-attachments/assets/ae6481e3-2338-4814-b600-db210a2e307b)

   - Click "OK" on all dialogs

#### 3.5 Restart Terminal or Computer

**⚠️ VERY IMPORTANT**: You must restart your terminal for changes to take effect.

- **Option 1**: Close and reopen Command Prompt/PowerShell/VS Code Terminal
- **Option 2**: Restart your computer (recommended for complete system refresh)

**Why this is critical**: Windows only reads environment variables when a process starts. Your existing terminal session has already loaded the old PATH variable and won't recognize the new entry until restarted.

### Step 4: Verify winget Installation (Final Check)

After restarting your terminal, open Command Prompt and run:
```cmd
winget --version
```

**Expected Output**: Version number (e.g., `v1.7.11261`)

- ✅ **If you see the version number**, proceed to **Step 5**
- ❌ **If you still see an error**, repeat Step 3.5 and ensure all terminal windows are fully closed before reopening

---

## Step 5: Install Astro CLI

Now that winget is verified and working, install the Astronomer Astro CLI.

### 5.1 Installation Command

Open Command Prompt or PowerShell and execute:

```cmd
winget install -e --id Astronomer.Astro
```

**Command Breakdown**:
- `winget install`: Windows Package Manager install command
- `-e`: Exact match flag (ensures precise package identification)
- `--id Astronomer.Astro`: The unique identifier for the Astro CLI package

---

## Step 6: Verify Astro CLI Installation

After installation completes, **restart your terminal** to ensure the PATH is updated.

### 6.1 Check Astro Version

Run the following command:

```cmd
astro version
```

**Expected Output**:

![Astro version output](https://github.com/user-attachments/assets/e2aca339-568f-40a2-a0af-9eb55cb36de4)

✅ **Success**: If you see version information, the Astro CLI is successfully installed and ready to use.

❌ **If command not found**: Restart your terminal again and ensure Docker Desktop is running.

---

## Getting Started with Astro

Once installation is complete, ensure Docker Desktop is running, then open VS Code or Command Prompt.

### Initialize a New Airflow Project

1. **Create a new directory** for your project and navigate to it
2. **Initialize Astro project**:
   ```cmd
   astro dev init
   ```

   ![Astro dev init](https://github.com/user-attachments/assets/40bc8d8e-eb87-479a-bcc9-0aff4fa6b5f1)

3. **Start Airflow locally**:
   ```cmd
   astro dev start
   ```

   ![Astro dev start](https://github.com/user-attachments/assets/8348ef0a-21bf-4a1e-b7a7-ada2f6cc30c1)

4. **Access Airflow UI**: Navigate to `http://localhost:8080` in your browser
   - Default credentials: `admin` / `admin`
   - **Note**: If you haven't created any DAGs yet, you may see errors or an empty DAG list. This is normal.

   ![Airflow UI initial view](https://github.com/user-attachments/assets/ced2040c-dc28-4f67-9bea-3a767f30ee25)

---

## Configuring Airflow Connections

To connect Airflow to external systems (databases, APIs), you need to configure connections.

### Setting Up a PostgreSQL Connection

1. **Access Admin Panel**:
   - In the Airflow UI, click **Admin** in the left sidebar
   - Click **Connections**
   - Click the **+** button to add a new connection

   ![Admin Connections menu](https://github.com/user-attachments/assets/14000a4f-c4c9-4b2a-87ec-47c62f962d7b)

2. **Configure PostgreSQL Connection**:
   - **Connection Id**: `my_postgres_connection` (or match what's defined in your `docker-compose.yml`)
   - **Connection Type**: Select `Postgres`
   - **Host**: Copy the container name from Docker Desktop
     - Open Docker Desktop
     - Find your PostgreSQL container
     - Copy the container name/hostname

   ![Docker container name](https://github.com/user-attachments/assets/d878ecdf-5c2e-4272-baf8-f630479017ec)

   - **Database**: Your database name
   - **Login**: Your database username
   - **Password**: Your database password
   - **Port**: `5432` (default PostgreSQL port)

   ![PostgreSQL connection configuration](https://github.com/user-attachments/assets/26958ed7-cfba-45cc-9806-e904d11da2c6)

3. Click **Save**

### Setting Up an HTTP/API Connection

1. **Add New Connection**:
   - Click the **+** button again
   - **Connection Id**: Name your API connection (e.g., `nasa_api`)
   - **Connection Type**: Select `HTTP`

2. **Configure API Connection**:
   - **Host**: Your API base URL
   - **Extra**: Paste any additional configuration in JSON format (API keys, headers, etc.)

   ![HTTP API connection](https://github.com/user-attachments/assets/aec157f4-39df-433e-99fa-f9bf842dbd18)

3. Click **Save**

---

## Running Your First DAG

After configuring connections and creating your DAG files:

1. **Trigger the DAG** in the Airflow UI
2. **Monitor execution**: Watch as tasks turn green upon successful completion

   ![Successful DAG run](https://github.com/user-attachments/assets/dfa81deb-6514-4e7c-b5b3-c1ea512c5525)

### Viewing Task Output

1. **Click on a task** (e.g., `extract_apod`)

   ![Task details](https://github.com/user-attachments/assets/4fbef749-c66e-4a63-8ec4-5001863c8adf)

2. **Click on XCom** to view data passed between tasks

   ![XCom data view](https://github.com/user-attachments/assets/7610808b-c807-4ee4-bbe9-4aa57221a392)

---

## Connecting to Your Database

To view and query your PostgreSQL database, you can use **DBeaver** or any other database client.

### Using DBeaver

1. Download and install DBeaver: [https://dbeaver.io/download/](https://dbeaver.io/download/)
2. Create a new PostgreSQL connection
3. Use the same connection details you configured in Airflow
4. Connect and explore your data

---

## Conclusion

You've successfully installed Astronomer (Astro) and set up Apache Airflow on Windows! You can now:

- Create and manage data pipelines using DAGs
- Connect to databases and APIs
- Monitor and debug workflow execution
- Scale your data orchestration needs

### Additional Resources

- **Astronomer Documentation**: [https://docs.astronomer.io/](https://docs.astronomer.io/)
- **Apache Airflow Documentation**: [https://airflow.apache.org/docs/](https://airflow.apache.org/docs/)
- **Astronomer Academy**: [https://academy.astronomer.io/](https://academy.astronomer.io/)

---

## Troubleshooting

### Common Issues

**Issue**: `astro` command not recognized
- **Solution**: Restart your terminal after installation. Ensure Docker Desktop is running.

**Issue**: Docker containers fail to start
- **Solution**: Check Docker Desktop is running and has sufficient resources allocated (Settings → Resources)

**Issue**: Cannot connect to `localhost:8080`
- **Solution**: Verify Docker containers are running with `docker ps`. Check for port conflicts.

**Issue**: Connection test fails
- **Solution**: Double-check connection credentials and host names. Ensure target services are running.

---

**Thank you for using this guide!** If you encounter any issues or have suggestions for improvements, please provide feedback.
