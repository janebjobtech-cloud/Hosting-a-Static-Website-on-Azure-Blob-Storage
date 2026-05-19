# Hosting-a-Static-Website-on-Azure-Blob-Storage
Full lab documentation for Lab 01 — Hosting a Static Website on Azure Blob Storage, covering architecture, step-by-step deployment phases, troubleshooting, clean-up, and key takeaways. Designed for your GitHub repository as a professional, replicable reference for the lab.

# ☁️ Lab 01 — Hosting a Static Website on Azure Blob Storage

![Azure](https://img.shields.io/badge/Azure-Blob%20Storage-0078d4?style=flat&logo=microsoftazure&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner-brightgreen?style=flat)
![Time](https://img.shields.io/badge/Estimated%20Time-30%20Minutes-blue?style=flat)
![Type](https://img.shields.io/badge/Hosting-Serverless%20%2F%20PaaS-orange?style=flat)

---

## 📋 Overview

This lab demonstrates how to deploy a **public-facing static website** on **Microsoft Azure** using **Azure Blob Storage** — no web servers, no VMs, no complex infrastructure. You configure a storage account, enable static website hosting, upload an HTML file, and your site is live on a globally accessible URL.

This is a foundational exercise in **PaaS (Platform as a Service)** and **Serverless** cloud architecture, illustrating how the cloud lets you focus on your content while Azure manages all underlying infrastructure.

---

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         AZURE ENVIRONMENT                           │
│                                                                     │
│   ┌──────────────────────────────────────────────────────────────┐  │
│   │           Resource Group: rg-lab01-[yourname]                │  │
│   │                                                              │  │
│   │   ┌──────────────────────────────────────────────────────┐   │  │
│   │   │        Storage Account: stlab01[yourname]            │   │  │
│   │   │                                                      │   │  │
│   │   │   ┌──────────────────────────────────────────────┐   │   │  │
│   │   │   │      Static Website Hosting (Enabled)        │   │   │  │
│   │   │   │                                              │   │   │  │
│   │   │   │   ┌──────────────────────────────────────┐   │   │   │  │
│   │   │   │   │     $web Container (Auto-created)    │   │   │   │  │
│   │   │   │   │                                      │   │   │   │  │
│   │   │   │   │         📄 index.html                │   │   │   │  │
│   │   │   │   │         📄 404.html (optional)       │   │   │   │  │
│   │   │   │   └──────────────────────────────────────┘   │   │   │  │
│   │   │   │                                              │   │   │  │
│   │   │   │   Primary Endpoint:                          │   │   │  │
│   │   │   │   https://stlab01[name].z13.web.core.        │   │   │  │
│   │   │   │               windows.net/                   │   │   │  │
│   │   │   └──────────────────────────────────────────────┘   │   │  │
│   │   └──────────────────────────────────────────────────────┘   │  │
│   └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                                   ▲
                                   │  HTTPS Request
                                   │
                          ┌────────┴────────┐
                          │   🌐  Internet   │
                          │     (Public)     │
                          └─────────────────┘
```

**Traffic Flow:**
```
User (Browser) ──► Public HTTPS URL ──► Azure Static Website Endpoint ──► $web Container ──► index.html
```

---

## 🛠️ Services & Concepts Used

| Service / Concept | Description |
|---|---|
| **Azure Resource Group** | Logical container for organizing related Azure resources |
| **Azure Blob Storage** | Object storage service used to store the HTML files |
| **Static Website Hosting** | Built-in Azure Storage feature that serves files over HTTP/HTTPS |
| **$web Container** | Auto-generated container that maps to your public website root |
| **LRS (Locally-Redundant Storage)** | Cost-effective redundancy tier; keeps 3 copies in one datacenter |
| **PaaS / Serverless** | No server to manage — Azure handles all infrastructure concerns |

---

## ✅ Prerequisites

Before starting this lab, ensure you have the following:

- [ ] An active **Azure Subscription** (Free Tier is sufficient)
- [ ] Access to the **Azure Portal** at [portal.azure.com](https://portal.azure.com)
- [ ] A basic **text editor** (Notepad, TextEdit, or VS Code)
- [ ] Completed **Week 1 Video Modules** (if following a course curriculum)

---

## 📌 Naming Conventions

Use the following naming pattern throughout this lab for consistency and easy identification.

| Resource | Name |
|---|---|
| Resource Group | `rg-lab01-[yourname]` |
| Storage Account | `stlab01[yourname]` |
| Region | `East US` |
| Container | `$web` (auto-created) |
| Index Document | `index.html` |
| Error Document | `404.html` |

> ⚠️ **Storage Account Naming Rules:** Must be globally unique across all of Azure, all lowercase, 3–24 characters, letters and numbers only — no hyphens or special characters.

---

## 🚀 Step-by-Step Deployment

### Phase 1 — Create the Resource Group

1. Log in to the [Azure Portal](https://portal.azure.com).
2. In the top search bar, type **Resource Groups** and select it.
3. Click **+ Create**.
4. Fill in the **Basics** tab:
   - **Subscription:** Select your active subscription
   - **Resource Group:** `rg-lab01-[yourname]`
   - **Region:** `(US) East US`
5. Click **Review + create** → **Create**.

---

### Phase 2 — Create the Storage Account

1. In the top search bar, type **Storage accounts** and select it.
2. Click **+ Create**.
3. Fill in the **Basics** tab:
   - **Resource Group:** `rg-lab01-[yourname]`
   - **Storage account name:** `stlab01[yourname]` *(e.g., `stlab01jhante`)*
   - **Region:** `(US) East US`
   - **Performance:** `Standard`
   - **Redundancy:** `Locally-redundant storage (LRS)`
4. Click **Review + create** → wait for validation → click **Create**.
5. Once deployment completes (~30 seconds), click **Go to resource**.

---

### Phase 3 — Enable Static Website Hosting

1. On the left-hand menu of your Storage Account, scroll to the **Data management** section.
2. Click **Static website**.
3. Toggle the switch from `Disabled` → **Enabled**.
4. Set **Index document name** to `index.html`.
5. Set **Error document path** to `404.html` *(optional but recommended)*.
6. Click **Save**.

> 🔑 **Important:** After saving, a **Primary endpoint URL** will be generated (e.g., `https://stlab01jhante.z13.web.core.windows.net/`). **Copy and save this URL** — it is your live website address.

---

### Phase 4 — Create the Website File

Open your text editor and paste the following HTML:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My First Cloud Site</title>
    <style>
      body {
        font-family: sans-serif;
        text-align: center;
        margin-top: 50px;
        background-color: #f0f0f0;
      }
      h1 { color: #0078d4; }
    </style>
  </head>
  <body>
    <h1>Hello from the Cloud!</h1>
    <p>This site is hosted on Azure Blob Storage.</p>
    <p>Deployed by: [Your Name]</p>
  </body>
</html>
```

Save the file to your desktop as **`index.html`** (exact filename, case-sensitive).

---

### Phase 5 — Upload to the $web Container

1. Return to the **Azure Portal** on your Storage Account.
2. On the left menu, click **Containers** (under *Data storage*).
3. Click the **`$web`** container (auto-created when you enabled static hosting).
4. Click **Upload** at the top of the page.
5. Browse to your `index.html` file on your desktop.
6. Click **Upload**.

---

### Phase 6 — Validate Your Deployment

1. Open a new browser tab.
2. Paste the **Primary endpoint URL** you saved in Phase 3.
3. Press **Enter**.
4. You should see your **"Hello from the Cloud!"** page rendered in the browser.

🎉 **Congratulations — you've just deployed a serverless website on Azure!**

---

## 🔍 Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| `404 - The requested content does not exist` | File not found in `$web` container | Confirm the file is named exactly `index.html` (case-sensitive) and was uploaded to the `$web` container specifically |
| `404` after correct upload | File uploaded to wrong container | Open **Containers** → verify the file is inside `$web`, not another container |
| `Storage account name is already taken` | Name not globally unique | Append random numbers to the end (e.g., `stlab01jhante99`) |
| Page loads but content is wrong | Cached browser content | Hard refresh with `Ctrl+Shift+R` (Windows) or `Cmd+Shift+R` (Mac) |

---

## 🧹 Clean Up Resources

> ⚠️ Always clean up lab resources to avoid unexpected charges on your Azure subscription.

1. In the Azure Portal, navigate to **Resource Groups**.
2. Click on `rg-lab01-[yourname]`.
3. Click **Delete resource group** at the top.
4. Type the resource group name to confirm.
5. Click **Delete**.

Deleting the resource group removes **all resources** inside it (storage account, containers, and files) in a single action.

---

## 💡 Key Takeaways

- Azure Blob Storage can serve static web content **without any web server or VM**.
- The **`$web` container** is the special container that maps directly to your static website endpoint.
- **LRS redundancy** is the most cost-effective option and is appropriate for non-production labs.
- Static website hosting on Azure is an example of **PaaS/Serverless** — you manage content, Azure manages infrastructure.
- Storage account names must be **globally unique across all of Azure**, not just your own subscription.

---

## 📁 Repository Structure

```
lab-01-azure-static-website/
│
├── README.md          # This file — full lab documentation
└── index.html         # Static website HTML file deployed to Azure
```

---

## 📚 References

- [Azure Blob Storage Documentation](https://learn.microsoft.com/en-us/azure/storage/blobs/)
- [Static Website Hosting in Azure Storage](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website)
- [Azure Resource Groups Overview](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal)
- [Azure Free Account](https://azure.microsoft.com/en-us/free/)

---

*Lab authored by Jhante Charles | Azure Cloud Labs Series | Lab 01 of Series*
