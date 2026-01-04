# Astronomer (Astro) Installation Guide for Windows

## What is Astronomer (Astro)?

**Astronomer (Astro)** is a managed platform for Apache Airflow that simplifies the deployment, scaling, and management of Airflow workflows. Key features include:

- **Managed Infrastructure**: Simplified Airflow deployment and scaling
- **Enterprise Features**: Built-in monitoring, security, and automation
- **Docker-Based Architecture**: Runs Airflow within Docker containers
- **Enhanced Developer Experience**: Streamlined CLI and deployment workflows

**Official Website**: [https://www.astronomer.io/](https://www.astronomer.io/)

---

## Prerequisites

### System Requirements

- **Operating System**: Windows 10 or later

### Required Software

**Docker Desktop for Windows**
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
<img width="1119" height="638" alt="image" src="https://github.com/user-attachments/assets/b6fb4f68-6843-4de9-939c-00b563662ef2" />

- ✅ **If you see a version number**, proceed to **Step 5**
- ❌ **If you see an error** ("winget is not recognized"), continue to **Step 3**

### Step 3: Install Windows Package Manager (If Required)

If winget is not recognized, follow these substeps:

#### 3.1 Install App Installer from Microsoft Store

1. Visit the Microsoft Store link: [App Installer](https://apps.microsoft.com/detail/9nblggh4nns1?hl=en-US&gl=IN)
2. Click "Get" or "Install"
3. Wait for installation to complete

#### 3.2 Verify Installation Directory

After installation, Windows creates the following folder:
```
C:\Users\<your-username>\AppData\Local\Microsoft\WindowsApps
```
<img width="1654" height="960" alt="image" src="https://github.com/user-attachments/assets/965d0030-a438-4b69-ae6d-1969cb206a99" />

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
   - *Or directly search for "environment" in the Windows search bar and select "Edit environment variables"*
  <img width="820" height="442" alt="image" src="https://github.com/user-attachments/assets/316da660-2a8d-4d59-9c48-775de6e63ce6" />

2. **Edit User PATH Variable**:
   - In the "User variables" section, select `Path`
   - Click "Edit"
   - Click "New"
   - Add the following path:
     ```
     C:\Users\<your-username>\AppData\Local\Microsoft\WindowsApps
     ```
     *(Replace `<your-username>` with your actual Windows username)*
     <img width="1039" height="730" alt="image" src="https://github.com/user-attachments/assets/ae6481e3-2338-4814-b600-db210a2e307b" />

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

After restarting your terminal, verify that the Astro CLI is properly installed.

### 6.1 Check Astro Version

Run the following command:

```cmd
astro version
```

**Expected Output**:
<img width="907" height="578" alt="image" src="https://github.com/user-attachments/assets/e2aca339-568f-40a2-a0af-9eb55cb36de4" />

✅ **Success**: If you see version information, the Astro CLI is successfully installed and ready to use.

❌ **If command not found**: Restart your terminal again and ensure Docker Desktop is running.

---

## Getting Started with Astro

Once installation is complete, make sure Docker is still running, then open VS Code.

1. **Initialize a new Airflow project**:
   ```cmd
   astro dev init
   ```
   <img width="1011" height="643" alt="image" src="https://github.com/user-attachments/assets/40bc8d8e-eb87-479a-bcc9-0aff4fa6b5f1" />

2. **Start Airflow locally**:
   ```cmd
   astro dev start
   ```
<img width="1126" height="725" alt="image" src="https://github.com/user-attachments/assets/8348ef0a-21bf-4a1e-b7a7-ada2f6cc30c1" />

3. **Access Airflow UI**: Navigate to `http://localhost:8080` in your browser. Since we haven't coded or created any DAGs yet, this will show an error. Don't worry—this is normal and we'll fix it later.
<img width="1874" height="897" alt="image" src="https://github.com/user-attachments/assets/ced2040c-dc28-4f67-9bea-3a767f30ee25" />

For more information, visit the [Astronomer Documentation](https://docs.astronomer.io/).
