# Product TDD-BDD

[![CI workflow](https://github.com/ShivamInnovates/Product-TDD-BDD/actions/workflows/workflow.yml/badge.svg)](https://github.com/ShivamInnovates/Product-TDD-BDD/actions/workflows/workflow.yml)

**Repository URL:** [https://github.com/ShivamInnovates/Product-TDD-BDD](https://github.com/ShivamInnovates/Product-TDD-BDD)

## Project Overview

**Product TDD-BDD** is an e-commerce Product Catalog RESTful microservice built with Python and Flask. This project applies Test-Driven Development (TDD) and Behavior-Driven Development (BDD) methodologies, paired with modern CI/CD pipelines, containerization, and cloud-native practices for the IBM DevOps and Software Engineering Professional Certificate capstone project.

The application allows users and services to manage products, execute full CRUD workflows, query the catalog by name, category, or availability, and administer products via an interactive web interface.

---

## Features

- **RESTful API Service**: Full CRUD lifecycle operations (Create, Read, Update, Delete) for product records.
- **Advanced Catalog Queries**: Query products by name, category (e.g., Cloths, Food, Housewares, Automotive, Tools), and availability status.
- **Web Administration UI**: Front-end single-page management dashboard backed by jQuery and Bootstrap.
- **Test-Driven Development (TDD)**: Comprehensive unit test suite with 96%+ statement coverage using `nosetests` and `factory_boy`.
- **Behavior-Driven Development (BDD)**: Acceptance test scenarios written in Gherkin syntax and automated using `behave` and Selenium WebDriver.
- **Code Quality**: Strict PEP 8 linting and validation via `flake8` and `pylint`.
- **Automated CI Workflow**: GitHub Actions pipeline triggering on push and pull requests to `main`.
- **Cloud-Native Tekton Tasks**: Reusable Tekton Pipeline tasks for workspace cleanup and automated unit testing.
- **Container & OpenShift Ready**: Production-grade Docker containerization adhering to non-root user permissions and OpenShift arbitrary UID requirements.

---

## Technology Stack

- **Language:** Python 3.9
- **Web Framework:** Flask 2.2.3, Gunicorn 20.1.0
- **Database & Persistence:** PostgreSQL, SQLite (for testing fallback), Flask-SQLAlchemy 3.0.2
- **Testing & Coverage:** Nose 1.3.7, Pinocchio 0.4.3, Coverage 7.1.0, Factory Boy 3.2.1
- **BDD Framework:** Behave 1.2.6, Selenium 4.1.0
- **Linting & Formatting:** Flake8 5.0.4, Pylint 2.17.4, Black 23.3.0
- **CI/CD & DevOps:** GitHub Actions, Tekton Pipelines v1/v1beta1, Docker, Red Hat OpenShift

---

## Project Structure

```text
.
├── .github/
│   └── workflows/
│       └── workflow.yml        # GitHub Actions CI workflow definition
├── .tekton/
│   └── tasks.yml               # Tekton Task definitions (cleanup, nose)
├── features/
│   ├── environment.py          # Behave environment & WebDriver setup
│   ├── products.feature        # BDD feature scenarios
│   └── steps/
│       ├── load_steps.py       # Background product data loader
│       └── web_steps.py        # Selenium UI interaction steps
├── service/
│   ├── __init__.py             # Flask application initialization
│   ├── config.py               # Application configuration settings
│   ├── models.py               # SQLAlchemy Product model & persistence
│   ├── routes.py               # REST API endpoints & route handlers
│   ├── common/
│   │   ├── cli_commands.py     # Custom Flask CLI commands
│   │   ├── error_handlers.py   # HTTP error handlers (400, 404, 405, 500)
│   │   ├── log_handlers.py     # Application logger configuration
│   │   └── status.py           # HTTP status code constants
│   └── static/                 # Static web assets (HTML, CSS, JS)
├── tests/
│   ├── factories.py            # FactoryBoy fake product generator
│   ├── test_cli_commands.py    # Flask CLI unit tests
│   ├── test_models.py          # Product model unit tests
│   └── test_routes.py          # API route integration tests
├── Dockerfile                  # Production container definition
├── Makefile                    # Developer automation targets
├── Procfile                    # Process file for cloud application runners
├── requirements.txt            # Pinned Python package dependencies
└── setup.cfg                   # Nose, coverage, and flake8 configurations
```

---

## Installation Instructions

### Prerequisites
- Python 3.9
- Docker (for running PostgreSQL or building container images)
- Firefox / Geckodriver (for running BDD Selenium scenarios)

### 1. Clone the Repository
```bash
git clone https://github.com/ShivamInnovates/Product-TDD-BDD.git
cd Product-TDD-BDD
```

### 2. Set Up Virtual Environment
```bash
python3.9 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install --upgrade pip wheel
pip install "setuptools<70"
pip install -r requirements.txt
```

---

## How to Run the Application

### Running with PostgreSQL (Local)
1. Start the PostgreSQL container:
   ```bash
   make db
   ```
2. Start the service using Gunicorn or Honcho:
   ```bash
   PORT=8080 gunicorn --workers=1 --bind 0.0.0.0:8080 --log-level=info service:app
   ```
3. Access the web interface in your browser:
   ```text
   http://localhost:8080
   ```
4. Check service health:
   ```bash
   curl http://localhost:8080/health
   ```

---

## How to Execute Unit Tests

Unit tests are executed using `nosetests` with coverage tracking:
```bash
nosetests
```
Alternatively, run with detailed coverage reports:
```bash
nosetests -vv --with-spec --spec-color --with-coverage --cover-package=service
```

> **Note:** Tests connect to PostgreSQL by default (`localhost:5432`). If PostgreSQL is not running, set `DATABASE_URI` to use SQLite:
> ```bash
> DATABASE_URI="sqlite:////tmp/test.db" nosetests
> ```

---

## How to Run Flake8 (Linting)

To verify PEP 8 compliance and code style across application and test code:
```bash
python -m flake8 service tests
```
Or with explicit arguments as defined in the `Makefile`:
```bash
flake8 service tests --count --max-complexity=10 --max-line-length=127 --statistics
```

For Pylint analysis:
```bash
pylint service tests --max-line-length=127
```

---

## How to Run BDD Tests

1. Ensure the PostgreSQL container and the Flask application are running:
   ```bash
   PORT=8080 gunicorn --workers=1 --bind 0.0.0.0:8080 --log-level=info service:app &
   ```
2. Run Behave:
   ```bash
   behave
   ```

---

## GitHub Actions CI Workflow

The project includes an automated continuous integration workflow defined in [`.github/workflows/workflow.yml`](.github/workflows/workflow.yml).

### Workflow Details:
- **Triggers:** Push to `main` and Pull Requests targeting `main`.
- **Services:** Spins up a PostgreSQL 18 container service (`postgres:alpine`).
- **Steps:**
  1. Checks out the repository code (`actions/checkout@v4`).
  2. Sets up Python 3.9 environment (`actions/setup-python@v5`).
  3. Installs pinned dependencies (`pip install -r requirements.txt`).
  4. **Lint with flake8**: Executes syntax and PEP 8 style validation.
  5. **Run unit tests with nose**: Runs the full unit test suite with coverage against the PostgreSQL database.

---

## Tekton Task Configuration

Reusable Tekton tasks are defined in [`.tekton/tasks.yml`](.tekton/tasks.yml) for cloud-native CI/CD on Kubernetes and OpenShift:

1. **`cleanup` Task**:
   - Safely cleans up the designated workspace directory (`$(workspaces.source.path)`) between pipeline runs using an Alpine Linux container to prevent file collision and stale artifacts.
2. **`nose` Task**:
   - Executes unit tests in a `python:3.9-slim` container on the pipeline workspace.
   - Accepts parameter `args` (default: `-v --with-spec --spec-color`).
   - Automatically installs dependencies and runs `nosetests`.

### Applying to an OpenShift / Kubernetes Cluster:
```bash
kubectl apply -f .tekton/tasks.yml
```

> **API Version Compatibility:** Tasks are authored using `apiVersion: tekton.dev/v1beta1`. For OpenShift Pipelines clusters enforcing `tekton.dev/v1`, change the header to `apiVersion: tekton.dev/v1`.

---

## Docker Instructions

### Build the Image
```bash
docker build --tag products:1.0 .
```

### Run the Container
```bash
docker run -d --name products-service \
  -p 8080:8080 \
  -e DATABASE_URI="postgresql://postgres:postgres@host.docker.internal:5432/postgres" \
  products:1.0
```

To test standalone with SQLite persistence:
```bash
docker run -d --name products-service \
  -p 8080:8080 \
  -e DATABASE_URI="sqlite:////tmp/test.db" \
  products:1.0
```

Verify the container is responding:
```bash
curl http://localhost:8080/health
```

---

## OpenShift Deployment Prerequisites

To deploy this application to Red Hat OpenShift:

1. **OpenShift CLI (`oc`)**: Install and authenticate to your OpenShift cluster (`oc login --token=... --server=...`).
2. **Project / Namespace**: Create or select a project (`oc new-project product-service`).
3. **Database Provisioning**: Deploy PostgreSQL via the OpenShift service catalog or PostgreSQL template:
   ```bash
   oc new-app postgresql-ephemeral --param POSTGRESQL_USER=postgres --param POSTGRESQL_PASSWORD=postgres --param POSTGRESQL_DATABASE=postgres
   ```
4. **Build and Deploy**:
   ```bash
   oc new-app --name products --strategy=docker --code=https://github.com/ShivamInnovates/Product-TDD-BDD.git
   ```
5. **Environment Configuration**: Set `DATABASE_URI` on the deployment:
   ```bash
   oc set env deployment/products DATABASE_URI="postgresql://postgres:postgres@postgresql:5432/postgres"
   ```
6. **Expose Route**:
   ```bash
   oc expose service/products
   ```
7. **Security / Permissions**: The container is pre-configured with non-root UID (`1000`) and group root permissions (`chmod -R g=u /app`) adhering to OpenShift restricted SCC policies.
