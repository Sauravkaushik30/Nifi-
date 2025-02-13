# Nifi-
# Apache NiFi Installation Guide

## Prerequisites

Ensure you have:
- A Linux-based system (Ubuntu, CentOS, macOS, etc.)
- **Java 21** installed

## Step-by-Step Installation

### Step 1: Install Java 21

Install Java 21 using:

```sh
sudo apt update && sudo apt install openjdk-21-jdk -y  # For Debian/Ubuntu
```

Verify installation:

```sh
java -version
```

---

### Step 2: Install Apache NiFi

Download the latest version of NiFi:

```sh
wget https://dlcdn.apache.org/nifi/2.2.0/nifi-2.2.0-bin.tar.gz
```

Extract the archive:

```sh
tar -xvzf nifi-2.2.0-bin.tar.gz
```

Move it to a preferred location:

```sh
mv nifi-2.2.0 /opt/nifi
cd /opt/nifi
```

---

### Step 3: Configure Java Path

1. Navigate to the `bin` directory:
   ```sh
   cd /opt/nifi/bin
   ```
2. Open the environment file:
   ```sh
   sudo nano nifi-env.sh
   ```
3. Add the following line:
   ```sh
   export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
   ```
4. Save and exit (`CTRL + X`, then `Y`, then `Enter`).

---

### Step 4: Configure NiFi Properties

1. Navigate to the `conf` directory:
   ```sh
   cd /opt/nifi/conf
   ```
2. Open `nifi.properties`:
   ```sh
   sudo nano nifi.properties
   ```
3. Locate `nifi.web.https.host` and set your IP:
   ```sh
   nifi.web.https.host=<your-ip-address>
   ```
4. Save and exit.

---

### Step 5: Generate SSL Certificate

Run the following command to generate a self-signed certificate:

```sh
keytool -genkeypair -alias nifi-cert \
  -keyalg RSA -keysize 2048 -validity 365 \
  -keystore /opt/nifi/conf/keystore.p12 \
  -storetype PKCS12 \
  -dname "CN=<your-ip>, OU=<your-organizational-unit>, O=<your-organization>, L=<your-city>, ST=<your-state>, C=<your-country>"
```

Set a strong password when prompted.

---

### Step 6: Set NiFi Admin Credentials

Run this command in the `bin` directory to set default credentials:

```sh
./nifi.sh set-single-user-credentials admin admin1234567
```

---

### Step 7: Start Apache NiFi

Start NiFi:

```sh
./nifi.sh start
```

Check status:

```sh
./nifi.sh status
```

Stop NiFi:

```sh
./nifi.sh stop
```

---

### Step 8: Access NiFi Web UI

Once NiFi is running, open:

```
https://<your-ip>:8443/nifi
```

Login using:

```
Username: admin
Password: admin1234567
```

---

## Additional Steps

- **Allow Firewall Access** (If needed):
  ```sh
  sudo ufw allow 8443/tcp
  ```

- **Enable NiFi on Boot (Optional)**:
  ```sh
  sudo ln -s /opt/nifi/bin/nifi.sh /etc/init.d/nifi
  sudo update-rc.d nifi defaults
  ```

---

## Troubleshooting

- If NiFi doesn’t start, check logs:
  ```sh
  cat /opt/nifi/logs/nifi-app.log
  ```
- Ensure the `nifi.properties` file has the correct IP.

---

## Conclusion

You have successfully installed and configured Apache NiFi. 🎉

For more details, visit the [official documentation](https://nifi.apache.org/docs.html).
