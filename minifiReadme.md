```markdown
# Apache MiNiFi Installation & Configuration Guide on Windows

This guide documents the complete process to install, configure, and run Apache MiNiFi (Java version) on a Windows machine. It covers Java installation, setting up environment variables, running MiNiFi from the command line, installing it as a Windows service for automatic startup, and configuring your flow.

---

## Prerequisites

- **Windows Operating System**
- **Administrator Privileges**
- **A Supported Java Development Kit (JDK)** (e.g., Oracle JDK, AdoptOpenJDK, or similar)

---

## 1. Install Java

### 1.1 Download and Install JDK

1. Download a JDK from one of these sources:
   - [Adoptium](https://adoptium.net/)
   - [Oracle](https://www.oracle.com/java/technologies/javase-downloads.html)
2. Install the JDK (e.g., it might install to `C:\Program Files\Java\jdk-21`).

### 1.2 Set Environment Variables

#### Set `JAVA_HOME`:

1. Right-click **This PC** (or **My Computer**) and select **Properties**.
2. Click **Advanced system settings**.
3. In the **System Properties** window, click the **Environment Variables…** button.
4. Under **System Variables**, click **New…** and enter:
   - **Variable Name:** `JAVA_HOME`
   - **Variable Value:** `C:\Program Files\Java\jdk-21`
5. Click **OK**.

#### Add Java to the `PATH`:

1. In the Environment Variables window, locate the `Path` variable under **System Variables** and click **Edit**.
2. Click **New** and add:
   ```
   %JAVA_HOME%\bin
   ```
3. Click **OK** to close all dialogs.

#### Verify Java Installation

Open a new Command Prompt or PowerShell window and run:

```powershell
java -version
```

You should see the installed Java version.

---

## 2. Download Apache MiNiFi

1. Visit the [Apache MiNiFi website](https://nifi.apache.org/minifi.html).
2. Download the latest MiNiFi Java binary (a `.zip` file).

---

## 3. Extract the MiNiFi Archive

- Extract the downloaded `.zip` file to a directory of your choice, for example:  
  `E:\nifi\minifi-2.2.0-bin`

---

## 4. Running MiNiFi from the Command Line

### 4.1 Open a Terminal

- Open **PowerShell** or **Command Prompt**.

### 4.2 Navigate to the `bin` Directory

```powershell
cd E:\nifi\minifi-2.2.0-bin\minifi-2.2.0\bin
```

### 4.3 Run MiNiFi

In **PowerShell**, use:

```powershell
.\run-minifi.bat
```

> **Note:**  
> - Use `.\` in PowerShell (not `./` as in Unix-like shells).  
> - If MiNiFi is already running as a service, you might see a message like:
>   ```
>   Apache MiNiFi is already running, listening to Bootstrap on port 61695
>   ```
>   This is expected if the service is active.

---

## 5. Installing MiNiFi as a Windows Service for Automatic Startup

Running MiNiFi as a service ensures that it will start automatically on system boot or after a restart.

### 5.1 Install the Service

1. **Open an Elevated Command Prompt:**
   - Search for `cmd`, right-click **Command Prompt**, and choose **Run as administrator**.
2. **Navigate to the `bin` Directory:**

   ```cmd
   cd E:\nifi\minifi-2.2.0-bin\minifi-2.2.0\bin
   ```

3. **Run the Service Installation Script:**

   ```cmd
   install-service.bat
   ```

### 5.2 Configure the Service for Automatic Startup

1. Open **Services**:
   - Press `Win + R`, type `services.msc`, and press Enter.
2. Locate the MiNiFi service (often named **Apache NiFi MiNiFi**).
3. Right-click the service and select **Properties**.
4. Under the **General** tab, set **Startup type** to **Automatic**.
5. Click **OK**.

### 5.3 (Optional) Configure Service Recovery Options

1. In the **Services** console, right-click your MiNiFi service and choose **Properties**.
2. Go to the **Recovery** tab.
3. Set the **First failure**, **Second failure**, and **Subsequent failures** to **Restart the Service**.
4. Adjust the **Reset fail count after** and **Restart service after** options as needed.
5. Click **OK**.

---

## 6. Flow Configuration

### 6.1 Configure Your Flow

- Place your `flow.json` (which defines your data flow, e.g., for file extraction) in the appropriate directory.
- In the `conf` folder, open `minifi.properties` and ensure it points to your flow file. For example:

  ```properties
  nifi.flow.configuration.file=conf/flow.json
  ```

### 6.2 Automatic Flow Loading

- When MiNiFi starts (either via the service or manually), it automatically reads the `flow.json` and begins processing the defined flow.

---

## 7. Verification & Troubleshooting

### 7.1 Verify MiNiFi is Running

- **Check the Service Status:**  
  Open **Services** (`services.msc`) and verify that the MiNiFi service is running.
  
- **Command Line Verification:**  
  Open an elevated Command Prompt and run:
  ```cmd
  sc query "Apache NiFi MiNiFi"
  ```
  Replace `"Apache NiFi MiNiFi"` with the actual service name if different.

- **Review Logs:**  
  Check the logs in the `logs` directory for confirmation of a successful startup and flow processing.

### 7.2 Note on Status Scripts

- The `status-minifi.bat` script is designed for foreground processes and may not correctly detect the service-managed instance.
- Rely on the Windows Services console or `sc query` for the accurate status.

---

## 8. Automatic Operation on Restart

With MiNiFi installed as a Windows service set to start automatically:
- Your MiNiFi agent and its flow (including file extraction as defined in `flow.json`) will start automatically after a shutdown or system restart.
- The service recovery options (if configured) ensure that MiNiFi will restart if it stops unexpectedly.

---

## Additional Resources

- [Apache MiNiFi Documentation](https://nifi.apache.org/minifi-docs/)
- [Apache MiNiFi GitHub Repository](https://github.com/apache/nifi-minifi)

---

This guide covers the installation, configuration, and automatic operation of Apache MiNiFi on Windows. For any further issues, consult the log files or refer to the official documentation.
```

---

Feel free to modify this `README.md` to better suit your environment or any additional customizations you might need.
