# Generating Self-Signed SSL Certificate in Linux  
*(and Creating & Managing a Certificate Authority on Windows)*

---

### Step 1: Verify HTTPS Is Not Enabled
1. Connect to the **Windows Desktop** machine.
2. Open the **Firefox** browser.
3. Navigate to:  
```

https://<your IP address>

```
- You will **not** be able to connect.
4. Now navigate to:  
```

http://<your IP address>

````
- You **can** connect successfully.

**Analysis:**  
This occurs because the web server is not configured to use HTTPS with an SSL certificate. This section will correct that issue.

---

### Step 2: Connect to the Ubuntu Console
1. Use the keyboard shortcut:
- **Windows/Linux:** `CTRL + ALT + SHIFT`
- **macOS:** `CTRL + CMD + SHIFT`
2. Open the menu and select **Windows Desktop** in the top-left corner.
3. From the dropdown, switch to the **Ubuntu Console** (`<your IP address>`).

---

### Step 3: Generate a Self-Signed SSL Certificate
Run the following command from the Ubuntu Console:
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/apache-selfsigned.key \
-out /etc/ssl/certs/apache-selfsigned.crt
````

**Note:**
You will be prompted for certificate information. The specific values do not affect the lab—enter any values that meet the length requirements and press **Enter** until completion.

This command creates:

* A private key
* A self-signed SSL certificate

---

### Step 4: Create an Apache HTTPS Configuration File

1. Create a new configuration file for port 443:

   ```bash
   sudo nano /etc/apache2/sites-available/<your IP address>.conf
   ```

2. Add the following content **exactly as shown**, then save and exit:

   ```apache
   <VirtualHost *:443>
       ServerName <your IP address>
       DocumentRoot /var/www/<your IP address>

       SSLEngine on
       SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
       SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
   </VirtualHost>
   ```

---

### Step 5: Create the HTTPS Web Root

1. Create a directory for the HTTPS site:

   ```bash
   sudo mkdir /var/www/<your IP address>
   ```

2. Create and edit the homepage:

   ```bash
   sudo nano /var/www/<your IP address>/index.html
   ```

3. Add the following content:

   ```html
   <h1>You are encrypted!</h1>
   ```

4. Save and exit the file.

---

### Step 6: Enable SSL and the New Site

1. Enable the SSL module:

   ```bash
   sudo a2enmod ssl
   ```

   * You should see a message instructing you to restart Apache.

2. Enable the HTTPS site:

   ```bash
   sudo a2ensite <your IP address>.conf
   ```

3. Restart Apache:

   ```bash
   sudo service apache2 restart
   ```

4. Verify the configuration:

   ```bash
   sudo apache2ctl configtest
   ```

   * The output should return **OK**.

---

### Step 7: Test HTTPS Connectivity

1. Return to the **Windows Desktop**.
2. Navigate to:

   ```
   https://<your IP address>
   ```
3. When the warning appears:

   * Select **Advanced**
   * Click **Accept the Risk and Continue**

### Result: You are now connected to the site securely via HTTPS.

---

## Part 2: Creating Your Own Certificate Authority on Windows

### Step 1: Install Active Directory Certificate Services

1. On the **Windows Desktop**, open the **Search Bar**.
2. Type **Server Manager** and open it.
3. Select **Manage** → **Add Roles and Features**.
4. On **Before You Begin**, click **Next**.
5. Select **Role-based or feature-based installation** → **Next**.
6. Keep default server selection → **Next**.
7. Select **Active Directory Certificate Services**.
8. When prompted, click **Add Features** → **Next**.
9. Click **Next** on:
<img width="823" height="606" alt="Screenshot 2026-02-26 at 10 11 35 AM" src="https://github.com/user-attachments/assets/67c78be1-4ce8-4d14-b7c9-eb7b4dcc4fba" />

   * Features page
   * AD CS page
10. On **Role Services**, select:

    * **Certification Authority**
    * **Certification Authority Web Enrollment**
11. Click **Add Features**, then **Next**.
12. Continue clicking **Next** until **Confirmation**.

<img width="827" height="600" alt="Screenshot 2026-02-26 at 10 11 54 AM" src="https://github.com/user-attachments/assets/75afb5f0-d3be-47fb-9c6f-e8bf7340da67" />

    
14. Select **Install**.
<img width="794" height="598" alt="Screenshot 2026-02-26 at 10 12 11 AM" src="https://github.com/user-attachments/assets/104e3513-8540-4947-b193-e38cfcf820f4" />

---

### Step 2: Configure Active Directory Certificate Services

1. After installation, select **Configure Active Directory Certificate Services**.
2. On **Credentials**, click **Next**.
3. On **Role Services**, select:
<img width="694" height="618" alt="Screenshot 2026-02-26 at 10 12 27 AM" src="https://github.com/user-attachments/assets/c985ec4c-0268-47aa-98f5-35217bd14871" />

   * Certification Authority
   * Certification Authority Web Enrollment
     *(Pause briefly between selections)*
4. Continue selecting **Next** until the **Confirmation** page.
5. Click **Configure**.
6. When complete, close all installation windows.

---

### Step 3: Verify the Certificate Authority

1. Return to **Server Manager Dashboard**.
2. Select **Tools** → **Certification Authority**.
3. Confirm that a local Certificate Authority is visible.

---

## Part 3: Configuring Your CA to Issue Certificates

### Step 1: Access Web Enrollment

1. Open **Internet Explorer**.

2. Navigate to:

   ```
   http://localhost/certsrv/
   ```

   > Web enrollment only works with Internet Explorer.

3. Select **Request a certificate**.

4. Choose **Web Browser Certificate**.

5. Observe the error message (HTTPS not enabled yet).

---

### Step 2: Enable HTTPS in IIS

1. Open **IIS Manager** from the Start Menu.
2. Navigate to the local server.
3. Double-click **Server Certificates**.
<img width="594" height="518" alt="Screenshot 2026-02-26 at 10 13 46 AM" src="https://github.com/user-attachments/assets/33b29d3f-a3ac-4df5-97b4-f27e921fc1b9" />
   
5. Select **Create Self-Signed Certificate**.
6. Enter:

   * Friendly name: `web_server`
7. Click **OK**.

---

### Step 3: Bind HTTPS to the Default Website

1. In IIS, expand **Sites**.
2. Select **Default Web Site**.
   <img width="829" height="429" alt="Screenshot 2026-02-26 at 10 14 06 AM" src="https://github.com/user-attachments/assets/8068060c-f260-48a8-b209-8812b124c685" />
3. Click **Bindings** → **Add**.
<img width="829" height="657" alt="Screenshot 2026-02-26 at 10 14 30 AM" src="https://github.com/user-attachments/assets/60495a1d-5e25-4898-8ddd-92731cd61943" />

4. Configure HTTPS and select the `web_server` certificate.
5. Click **OK**, then **Close**.

---

### Step 4: Verify Secure Web Enrollment

1. Return to **Internet Explorer**.
2. Navigate to:

   ```
   https://localhost/certsrv/
   ```
3. Select **More information** → **Go on to the webpage**.

---

## Part 4: Issuing and Revoking Certificates

### Step 1: Submit a Certificate Request

1. Select **Request a certificate** → **Web Browser Certificate**.
2. Fill in the form with example data:

   * **Name:** test
   * **E-Mail:** [test@gmail.com](mailto:test@gmail.com)
   * **Company:** someCompany
   * **Department:** Information Technology
   * **City:** Miami
   * **State:** Florida
   * **Country/Region:** US
3. Click **Submit**.
4. Record the **Request ID**.

---

### Step 2: Issue the Certificate

1. Open **Server Manager** → **Tools** → **Certification Authority**.
2. Expand the CA.
3. Select **Pending Requests**.
4. Right-click the request → **All Tasks** → **Issue**.
5. Confirm the certificate appears under **Issued Certificates**.

---

### Step 3: Revoke a Certificate

1. Right-click the issued certificate.
2. Select **All Tasks** → **Revoke Certificate**.
3. Choose **Certificate Hold** as the reason.
4. Click **Yes**.

---

### Step 4: Unrevoke the Certificate

1. Navigate to **Revoked Certificates**.
2. Right-click the certificate.
3. Select **All Tasks** → **Unrevoke Certificate**.
4. Confirm it returns to **Issued Certificates**.

---

## Final Outcome:

* Enabled HTTPS on Linux with a self-signed certificate
* Created a Windows Certificate Authority
* Issued, revoked, and reinstated certificates

```
```
