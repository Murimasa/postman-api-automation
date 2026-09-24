# 🚀 Restful Booker API Automation Framework

[![API Regression Pipeline (Newman)](https://github.com/Murimasa/postman-api-automation/actions/workflows/newman.yml/badge.svg)](https://github.com/Murimasa/postman-api-automation/actions/workflows/newman.yml)

Автоматизированный тестовый фреймворк для сквозного тестирования API сервиса **Restful Booker** с использованием **Postman**, **Newman** и **GitHub Actions**.

---

## 📌 Архитектура и возможности

- **End-to-End CRUD цикл**: Проверка полного жизненного цикла сущности бронирования (`Create` -> `Read` -> `Update` -> `Delete`) с динамической передачей параметров между запросами.
- **Data-Driven Testing (DDT)**: Параметризованное создание сущностей из внешнего набора данных без использования коммерческих ограничений Postman Runner.
- **Динамическое управление состоянием**: Автоматический сбор и передача токенов авторизации через заголовки `Cookie` и извлечение динамических ID (`bookingid`).
- **Расширенная валидация**: Проверка статус-кодов, соответствия JSON-схемы, значений полей в payload и ответов сервера.
- **Интерактивные HTML-отчеты**: Визуализация результатов каждого запуска через дашборд `newman-reporter-htmlextra`.
- **CI/CD Автоматизация**: Изолированные шаги сборки, запуск тестов на Ubuntu runner и публикация артефактов при каждом коммите и пулл-реквесте.

---

## 🗂 Структура проекта

```text
postman-api-automation/
├── .github/
│   └── workflows/
│       └── newman.yml          # CI/CD пайплайн для GitHub Actions
├── collections/
│   └── Restful Booker API Automation.postman_collection.json
├── environments/
│   └── Restful-Booker-Prod.postman_environment.json
├── data/
│   └── testdata.json           # Набор тестовых данных для DDT
├── .gitignore
└── README.md

Prerequisites
To run these tests locally, ensure you have:

Node.js (version 18 or higher)

Newman and the HTML Extra Reporter installed globally:

Bash
npm install -g newman newman-reporter-htmlextra
💻 Running Tests Locally
1. Execute Core CRUD Suite (excluding DDT)
Bash
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
2. Execute Data-Driven Test Suite (DDT)
Bash
newman run "collections/Restful Booker API Automation.postman_collection.json" \
  -e "environments/Restful-Booker-Prod.postman_environment.json" \
  -d "data/testdata.json" \
  --folder "DDT - Create Booking" \
  -r cli,htmlextra \
  --reporter-htmlextra-export newman-report/ddt-report.html
📊 Viewing and Downloading Test Reports (CI/CD)
Interactive HTML reports are generated automatically on every push or pull request to the main branch:

Navigate to the repository's Actions tab.

Select the latest successful run of API Regression Pipeline (Newman).

Scroll down to the Artifacts section at the bottom of the page.

Download the newman-html-reports archive.

Extract the ZIP archive and open the HTML files in any browser:

crud-report.html — Full request/response inspection for the end-to-end CRUD suite.

ddt-report.html — Per-iteration execution breakdown for parameterized booking creation.
