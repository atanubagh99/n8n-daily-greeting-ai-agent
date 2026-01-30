# 💌 The Romantic AI Agent: "Love on Autopilot"
### A Self-Hosted, Zero-Cost Daily Greeting Bot using n8n + Ollama

![n8n](https://img.shields.io/badge/Workflow-n8n-FF6D5A?style=for-the-badge&logo=n8n)
![Ollama](https://img.shields.io/badge/AI-Ollama-white?style=for-the-badge&logo=ollama)
![Status](https://img.shields.io/badge/Status-Active-success?style=for-the-badge)

> **"Never forget to say 'Good Morning' or 'Good Night' again."**

This project is a complete automation agent that wakes up twice a day, uses a local AI model (Llama 3.2) to generate a unique, human-sounding romantic message, and emails it to your partner. It runs entirely on your local machine and costs **$0.00**.

---

## 🏗️ Architecture

The workflow follows a logical "Y" structure. It uses two separate time triggers that feed into a single AI brain, ensuring the efficient reuse of logic.

![Workflow Diagram](./image_d39304.png)
*(Note: Upload the screenshot of your workflow here)*

**The Logic Flow:**
1.  **Triggers ⏰:** Wakes up at **8:00 AM** and **11:00 PM**.
2.  **Context Engine 🧠:** Sets the "Time of Day" and a specific "Subject Line" (e.g., "Good morning sunshine" vs. "Sweet dreams").
3.  **The Brain (Local AI) 🦙:** Connects to **Ollama** running locally. It uses a custom "Anti-Cliché" prompt to ensure messages sound like *you*, not a robot.
4.  **Delivery 📨:** Sends the email via **Gmail SMTP** with a clean, professional signature.

---

## 🛠️ Prerequisites

Before you start building, ensure you have these three components ready:

### 1. n8n (Self-Hosted)
You need n8n running locally.
* [Official Installation Guide](https://docs.n8n.io/hosting/)

### 2. Ollama & Llama 3.2
This is the "Brain" of the operation.
1.  Download and install [Ollama](https://ollama.com/).
2.  Open your terminal and pull the model:
    ```bash
    ollama run llama3.2
    ```
3.  Ensure Ollama is running in the background.

### 3. Gmail "App Password"
**Crucial:** You cannot use your normal Google login password.
1.  Go to your [Google Account Security page](https://myaccount.google.com/security).
2.  Enable **2-Step Verification**.
3.  Search for **"App Passwords"**.
4.  Create a new one named "n8n" and **copy the 16-character code**. You will need this later.

---

## 🚀 Step-by-Step Implementation Guide

Follow these steps to build the workflow from scratch (or configure the provided JSON file).

### Phase 1: The Triggers (Setting the Schedule)
We need two separate triggers to start the process.
1.  Add a **Schedule Trigger** node. Set it to **8:00 AM**.
2.  Add a second **Schedule Trigger** node. Set it to **11:00 PM** (23:00).

### Phase 2: Context Setting (Defining the Vibe)
The AI needs to know *what* to write about. We use "Edit Fields" (Set) nodes for this.
1.  Connect a **Set** node to the Morning trigger.
    * Variable `time_of_day`: "Morning"
    * Variable `subject_line`: "Good morning, sunshine ☀️"
2.  Connect a separate **Set** node to the Night trigger.
    * Variable `time_of_day`: "Night"
    * Variable `subject_line`: "A little night note for you 🌙"

### Phase 3: The AI Brain (Ollama)
This is where the magic happens.
1.  Add a **Basic LLM Chain** node.
2.  Connect **Ollama Chat Model** to the chain.
3.  **Network Config (The "Gotcha"):**
    * If n8n is in Docker, use Base URL: `http://host.docker.internal:11434`
    * If n8n is native, use Base URL: `http://localhost:11434`
4.  **The Prompt:** Use a strict prompt to keep it human.
    > *"Write a short text. Do not use clichés. Do not be poetic. Keep it casual. Output ONLY the message body."*
5.  **Temperature:** Set to **0.9** in Options. This ensures the message is different every single day.

### Phase 4: The Delivery (Email)
1.  Add a **Send Email** node.
2.  **Credential Setup:** Select **SMTP**.
    * **User:** Your Gmail address.
    * **Password:** The 16-character App Password.
    * **Host:** `smtp.gmail.com`
    * **Port:** `587`
    * **SSL/TLS:** **OFF** (Important!).
3.  **Formatting:**
    * **From:** `Your Name <your-email@gmail.com>` (This format hides the email address).
    * **Body:** Map the AI output and append your signature manually (`{{ $json.text }}\n\nLove, Atanu`).

---

## ⚠️ Common Issues & "Gotchas"
*Read this if your workflow isn't working!*

### 1. Removing the "Sent automatically with n8n" Footer
By default, n8n adds branding to the bottom of emails.
* **Fix:** In the **Send Email** node, scroll to **Options** → Add **"Append n8n Attribution"** → Toggle it to **FALSE** (Grey).

### 2. Connection Refused (Ollama)
If n8n says it can't find Ollama, it's usually a Docker networking issue.
* **Fix:** Do not use `localhost`. Use `host.docker.internal` (Mac/Windows) or your bridge IP `172.17.0.1` (Linux).

### 3. "Authentication Failed" (Gmail)
* **Fix:** You are likely using your login password. You **must** use the App Password generated in Google Security settings.

### 4. Workflow Not Running Automatically
* **Fix:** You must click the **Activate / Publish** switch in the top right corner of the n8n editor. The background must be green.
* **Note:** Since this is local, your computer **must be awake** at 8:00 AM and 11:00 PM for the automation to fire.

---

## 🌟 Final Production Check
Once built, do a final test run:
1.  Click **Execute Workflow**.
2.  Check your "Sent" folder in Gmail.
3.  Verify the signature looks clean and the message sounds human.
4.  Set the workflow to **Active**.

**Enjoy your new life of automated romance! ❤️**

---
*Built with n8n and Llama 3.2*