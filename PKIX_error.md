```markdown
# Resolving SSLHandshakeException (PKIX Path Building Failed) in Apache NiFi

This document outlines the steps to resolve the following error encountered when configuring a Remote Process Group (RPG) in NiFi:

```
javax.net.ssl.SSLHandshakeException: PKIX path building failed: 
sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
```

The error occurs because NiFi is using a self‑signed certificate that is not trusted by the JVM's (or NiFi's custom) truststore. Follow these steps to export the certificate and import it into the truststore.

---

## Prerequisites

- **Apache NiFi 2.2.0** is installed on your Linux machine.
- The NiFi keystore and truststore are configured via `nifi.properties`.
- Your custom truststore is in PKCS12 format (e.g., `truststore.p12`).
- `keytool` and `openssl` are available on your system.

---

## Steps

### 1. Export the Self-Signed Certificate

NiFi uses a self‑signed certificate. First, export the certificate from the NiFi keystore.

1. **List the certificate entry:**

   ```bash
   keytool -list -keystore /home/identiqa/nifiadmin/nifi-2.2.0/conf/keystore.p12 -storetype PKCS12
   ```

   *Note: Look for the alias (e.g., `nifi-cert`).*

2. **Export the certificate to PEM format:**

   ```bash
   keytool -export -alias nifi-cert -keystore /home/identiqa/nifiadmin/nifi-2.2.0/conf/keystore.p12 -storetype PKCS12 -rfc -file /home/identiqa/nifiadmin/nifi-2.2.0/conf/nifi_cert.pem
   ```

   This creates the file `nifi_cert.pem` in the NiFi configuration folder.

---

### 2. Import the Certificate into the PKCS12 Truststore

NiFi is configured to use a custom truststore (in PKCS12 format). Import the certificate into this truststore.

1. **Locate the Truststore:**

   Check your `nifi.properties` file for entries similar to:

   ```properties
   nifi.security.truststore=./conf/truststore.p12
   nifi.security.truststoreType=PKCS12
   nifi.security.truststorePasswd=yourTruststorePassword
   ```

2. **Import the certificate:**

   Run the following command (adjust the path and password as needed):

   ```bash
   sudo keytool -import -alias nifi -file /home/identiqa/nifiadmin/nifi-2.2.0/conf/nifi_cert.pem \
     -keystore /home/identiqa/nifiadmin/nifi-2.2.0/conf/truststore.p12 \
     -storetype PKCS12 -storepass yourTruststorePassword
   ```

   When prompted with “Trust this certificate? [no]:”, type **yes**.

3. **Verify the Import:**

   List the contents of your truststore to ensure the certificate is present:

   ```bash
   sudo keytool -list -keystore /home/identiqa/nifiadmin/nifi-2.2.0/conf/truststore.p12 \
     -storetype PKCS12 -storepass yourTruststorePassword | grep nifi
   ```

   You should see an entry with the alias `nifi`.

---

### 3. Restart NiFi

Restart NiFi to ensure it picks up the updated truststore.

```bash
cd /home/identiqa/nifiadmin/nifi-2.2.0/bin
./nifi.sh restart
```

---

### 4. Test the Remote Process Group Connection

1. **In the NiFi UI:**
   - Navigate to `https://10.1.1.5:8443/nifi`.
   - Add a Remote Process Group (RPG) on the canvas.
   - Configure the RPG with the target URL:
     ```
     https://10.1.1.5:8443/nifi
     ```

2. **Verify the Connection:**
   - The RPG should now connect successfully without any SSL handshake errors.
   - Check the NiFi logs (`nifi-app.log`) for any further errors.

---

## Troubleshooting

- **Certificate Chain:**  
  Since this is a self‑signed certificate, no additional intermediate certificates are required. However, ensure the certificate’s Common Name (CN) matches the IP address (`10.1.1.5`).

- **SSL Debugging:**  
  If problems persist, enable detailed SSL debugging by adding the following line to `bootstrap.conf`:
  
  ```properties
  java.arg.20=-Djavax.net.debug=ssl,handshake
  ```
  
  Then, restart NiFi and inspect the logs for more details.

---

## Conclusion

Following these steps will resolve the SSL handshake error by ensuring NiFi’s JVM trusts the self‑signed certificate used for Site-to-Site communication.

```

---

You can commit this README.md file to your Git repository to document the resolution process.
