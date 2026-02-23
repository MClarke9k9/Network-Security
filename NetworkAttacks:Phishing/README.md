# In this Network and Security lesson we will learn how to fight against Phishing and Malicious Links.


## Cloning a Website for Credential Harvesting (Educational Simulation)

> ⚠️ **Ethical Use Notice**  
> This guide is written **for educational and defensive awareness purposes only**.
> It demonstrates, at a high level, how social-engineering attacks are structured so defenders can better recognize and mitigate them.
> Only perform these activities in environments you own or have explicit permission to test.

---

## Project Overview

SomeSite has identified an increase in phishing attempts targeting users through fake login pages that imitate trusted services.  
This project walks through a **simulated** credential-harvesting scenario using the Social Engineering Toolkit (SET) to show:

- How phishing sites are created
- How credentials can be captured
- Why user awareness and defensive controls are critical

All identifiers have been generalized and sanitized.

---

## Step-by-Step Guide

### 1. Prepare the Attack Framework

1. Open a terminal on your testing system.
2. Verify the system is ready by checking its status.
---
<img width="363" height="60" alt="Screenshot 2026-02-22 at 9 22 38 PM" src="https://github.com/user-attachments/assets/9453927f-4afa-4f62-ac94-0f78a89f7c5d" />

---

4. Download the Social Engineering Toolkit at https://github.com/trustedsec/social-engineer-toolkit
5. Start the Social Engineering Toolkit by finding the location you downloaded it in:
   ```bash
   sudo <the file path>


6. Accept the terms of service when prompted.

---

### 2. Navigate the SET Menus

1. From the main menu, select:

   * **Social-Engineering Attacks**
   * <img width="603" height="747" alt="Screenshot 2026-02-22 at 9 25 47 PM" src="https://github.com/user-attachments/assets/b6e2ce66-7f9f-44f2-8fe7-8676d0ff8b9d" />

2. Choose:

   * **Website Attack Vectors**
   * <img width="605" height="696" alt="Screenshot 2026-02-22 at 9 26 51 PM" src="https://github.com/user-attachments/assets/eccc4b64-629f-4401-9cc6-73b3439174c1" />

3. Select:

   * **Credential Harvester Attack Method**
   * <img width="600" height="670" alt="Screenshot 2026-02-22 at 9 27 15 PM" src="https://github.com/user-attachments/assets/019d2637-09cc-42fa-9608-11d1bc6c0e3a" />

4. Choose:

   * **Web Templates**
   * <img width="601" height="357" alt="Screenshot 2026-02-22 at 9 27 53 PM" src="https://github.com/user-attachments/assets/982a66ca-21e3-475d-b896-4fcb473cc255" />


> **Why this matters:**
> Credential harvesting relies on cloning familiar login pages to exploit user trust.

---

### 3. Configure the Credential Harvester

1. When prompted for the **POST back address**, enter:

   ```
   <your website in question>
   ```
2. Select a well-known template (for example, a popular email or search provider).
<img width="640" height="140" alt="Screenshot 2026-02-22 at 9 29 48 PM" src="https://github.com/user-attachments/assets/d31039cd-c5bb-4084-8ece-dc2c55333fc1" />

> **Security insight:**
> Attackers depend heavily on brand recognition to increase success rates.

---

### 4. Access the Cloned Website

1. On a separate test machine or browser, open a web browser.
2. Navigate to:

   ```
   <your website in question>
   ```
3. The page should visually resemble the legitimate service.

---

### 5. Simulate User Interaction

1. Enter **test credentials only**, such as:

   * Username: `user@example.com`
   * Password: `examplepass`
2. Submit the form.

> ⏳ The page may take a moment to respond.

---

### 6. Observe Captured Data

1. Return to the terminal running SET.
2. Review the output logs.
3. Confirm that the submitted credentials appear in the captured data.

> **Defensive takeaway:**
> This demonstrates how quickly sensitive information can be exposed through phishing.

---

### 7. Stop the Harvester

1. Stop the credential harvester:

   * Press `Ctrl + C`
2. Return to the main SET menu.

---

## Key Security Lessons

* Phishing attacks rely more on **human trust** than technical exploits.
* Even basic credential harvesters can be highly effective.
* Strong defenses include:

  * User awareness training
  * Email and web filtering
  * Multi-factor authentication (MFA)
  * Incident response readiness

---

## Conclusion

You have completed a **sanitized, educational walkthrough** of how a cloned website can be used to harvest credentials.
Understanding this process helps security professionals:

* Recognize phishing indicators faster
* Educate users more effectively
* Implement controls that reduce real-world risk

Use this knowledge to **strengthen defenses**, not exploit trust.
