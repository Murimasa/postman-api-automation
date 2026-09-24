# 🚀 Restful Booker API Automation Framework

[![API Regression Pipeline (Newman)](https://github.com/Murimasa/postman-api-automation/actions/workflows/newman.yml/badge.svg)](https://github.com/Murimasa/postman-api-automation/actions/workflows/newman.yml)
[![Postman](https://img.shields.io/badge/Postman-v10+-orange?logo=postman&logoColor=white)](https://www.postman.com/)
[![Newman](https://img.shields.io/badge/CLI-Newman-FF6C37?logo=npm&logoColor=white)](https://learning.postman.com/docs/collections/using-newman-cli/command-line-integration-with-newman/)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub_Actions-2088FF?logo=github-actions&logoColor=white)](https://github.com/features/actions)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An end-to-end automated testing suite for the **Restful Booker API** service, built using **Postman**, executed headless via **Newman**, and orchestrated in an automated **GitHub Actions CI/CD pipeline** with rich HTML reporting.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Key Features](#-key-features)
- [Project Architecture](#-project-architecture)
- [Prerequisites](#-prerequisites)
- [Local Execution](#-local-execution)
- [CI/CD & HTML Test Reports](#-cicd--html-test-reports)
- [Test Strategy & Verification](#-test-strategy--verification)

---

## 🔍 Overview

This repository demonstrates enterprise-grade API regression testing and test data management. It resolves common Postman automation challenges such as environment state isolation, dynamic token extraction, and free-tier Data-Driven Testing (DDT) without commercial license lock-in.

---

## ✨ Key Features

- **Full Lifecycle E2E CRUD Coverage**: Validates state progression (`Ping` -> `Auth` -> `Create` -> `Read` -> `Update` -> `Delete`).
- **Data-Driven Testing (DDT)**: Parameterized data iteration driven by external JSON datasets (`data/testdata.json`), executed locally and in CI without paid Postman runner limitations.
- **Dynamic State Management**: Inter-request variable passing (`token` injection via headers and automatic storage of generated `booking_id`).
- **Rich Dashboard Reporting**: Automated report generation with interactive drill-down views using `newman-reporter-htmlextra`.
- **Headless CI/CD Pipeline**: GitHub Actions workflow running on `ubuntu-latest`, validating both CRUD workflows and parameterized suites in isolated jobs.

---

## 📁 Project Architecture

```text
postman-api-automation/
├── .github/
│   └── workflows/
│       └── newman.yml          # GitHub Actions CI/CD regression pipeline
├── collections/
│   └── Restful Booker API Automation.postman_collection.json
├── environments/
│   └── Restful-Booker-Prod.postman_environment.json
├── data/
│   └── testdata.json           # External dataset for DDT execution
├── .gitignore                  # Git exclusions for node_modules and reports
└── README.md                   # Repository documentation
```

---

## ⚙️ Prerequisites

Before running tests locally, ensure the following dependencies are installed:

- [Node.js](https://nodejs.org/) (`v18.x` or higher)
- [npm](https://www.npmjs.com/) (`v9.x` or higher)

Install **Newman** and the **HTML Extra Reporter** globally:

```bash
npm install -g newman newman-reporter-htmlextra
```

---

## 💻 Local Execution

Run the commands below from the root of the project directory.

### 1. Execute Full E2E CRUD Suite

Runs the entire CRUD lifecycle sequentially:

```bash
newman run "collections/Restful Booker API Automation.postman_collection.json" \
  -e "environments/Restful-Booker-Prod.postman_environment.json" \
  -n 1 \
  --folder "Ping / HealthCheck" \
  --folder "Auth - Create Token" \
  --folder "POST /booking" \
  --folder "Get Booking by ID" \
  --folder "Update Booking (PUT)" \
  --folder "Delete Booking" \
  -r cli,htmlextra \
  --reporter-htmlextra-export newman-report/crud-report.html
```

### 2. Execute Data-Driven Test Suite (DDT)

Runs parameterized booking creation across 3 independent dataset iterations:

```bash
newman run "collections/Restful Booker API Automation.postman_collection.json" \
  -e "environments/Restful-Booker-Prod.postman_environment.json" \
  -d "data/testdata.json" \
  --folder "DDT - Create Booking" \
  -r cli,htmlextra \
  --reporter-htmlextra-export newman-report/ddt-report.html
```

---

## 📊 CI/CD & HTML Test Reports

Every `push` or `pull_request` triggers the automated pipeline configured in `.github/workflows/newman.yml`.

### How to Access Generated Reports:

1. Open the [**Actions**](https://github.com/Murimasa/postman-api-automation/actions) tab of this repository.
2. Select the latest workflow run: **`API Regression Pipeline (Newman)`**.
3. Scroll down to the **Artifacts** section at the bottom of the page.
4. Download the **`newman-html-reports`** archive.
5. Extract the `.zip` archive and open the files in any modern web browser:
   - `crud-report.html`: Detailed assertions, timing metrics, and payloads for the primary CRUD flow.
   - `ddt-report.html`: Iteration breakdown showing each dataset record evaluated.

---

## 🧪 Test Strategy & Verification

| Step | HTTP Method | Endpoint | Primary Assertion |
| :--- | :--- | :--- | :--- |
| **Health Check** | `GET` | `/ping` | `201 Created` server readiness check |
| **Auth** | `POST` | `/auth` | Token issued (`200 OK`) and stored in runtime environment |
| **Create Booking** | `POST` | `/booking` | `200 OK`, JSON schema validated, `bookingid` captured |
| **Read Booking** | `GET` | `/booking/{{booking_id}}` | Data integrity matched with created record |
| **Update Booking** | `PUT` | `/booking/{{booking_id}}` | Full payload update verified with authorized cookie |
| **Delete Booking** | `DELETE` | `/booking/{{booking_id}}` | `201 Created` resource removal verification |
| **DDT Iterations** | `POST` | `/booking` | Values matched against external JSON records across iterations |