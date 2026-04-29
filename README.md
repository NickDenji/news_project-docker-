# Django News Application

## Overview

This is a Django-based news application that allows **journalists to create articles**, **editors to approve them**, and **readers to view approved content**.

The project demonstrates:

* Role-based access control
* REST API design using Django REST Framework
* Containerisation with Docker
* Proper database configuration using MariaDB

---

## Features

* Role-based access control (Reader, Journalist, Editor)
* Article creation, update, and deletion via REST API
* Article approval workflow (Editor-controlled)
* Subscription-based article filtering
* Docker container support
* Automated unit testing

---

## Technologies Used

* Python
* Django
* Django REST Framework
* MariaDB (primary database)
* SQLite (development-only fallback)
* HTML (Django Templates)
* Docker

---

## Repository Setup

### Clone the repository

```bash
git clone https://github.com/NickDenji/news_project_docker.git
cd news_project
```

---

## Environment Variables & Secrets

This project requires sensitive values (such as Django `SECRET_KEY` and email credentials).

**These are NOT included in the repository for security reasons.**

### Setup

Create a `.env` file in the project root:

```bash
touch .env
```

Add the following:

```env
SECRET_KEY=your-secret-key
DEBUG=True
EMAIL_HOST_USER=your-email
EMAIL_HOST_PASSWORD=your-password
```

Ensure `.env` is included in `.gitignore`.

---

## Local Development Setup (venv)

### 1. Create a virtual environment

```bash
python -m venv .venv
```

Activate it:

**Windows**

```bash
.venv\Scripts\activate
```

**Mac/Linux**

```bash
source .venv/bin/activate
```

---

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

---

## Database Configuration (IMPORTANT)

This project is designed to use **MariaDB as the primary database**.

SQLite is included only as a **development fallback** and should not be used in production environments.

---

### MariaDB Setup (Recommended)

1. Install MariaDB locally

2. Create a database:

```sql
CREATE DATABASE news_db;

CREATE USER 'news_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON news_db.* TO 'news_user'@'localhost';
FLUSH PRIVILEGES;
```

3. Install the required database driver:

```bash
pip install mysqlclient
```

4. Update `settings.py`:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'news_db',
        'USER': 'news_user',
        'PASSWORD': 'your_password',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

---

### SQLite (Development Only)

If you do not want to set up MariaDB, you can use SQLite for quick development.

Update `settings.py`:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

⚠️ SQLite is not suitable for production use.

---

### Apply migrations

```bash
python manage.py migrate
```

---

### Create superuser

```bash
python manage.py createsuperuser
```

---

### Run development server

```bash
python manage.py runserver
```

---

### Access the application

* Main site: http://127.0.0.1:8000/
* Admin panel: http://127.0.0.1:8000/admin/
* API: http://127.0.0.1:8000/api/articles/

---

## Run with Docker

This project can be run using Docker.

⚠️ By default, Docker uses SQLite for simplicity.
For a production-ready setup, you should configure MariaDB separately.

### Build the container

```bash
docker build -t news-app .
```

---

### Run the container

```bash
docker run -p 8000:8000 news-app
```

This will automatically:

* Install dependencies
* Apply migrations
* Start the Django development server

---

### Access the application

http://localhost:8000

---

## Database Notes

* MariaDB is the primary database for this project
* SQLite is provided only for development convenience
* Production deployments should always use MariaDB

---

## User Roles

| Role       | Permissions                                |
| ---------- | ------------------------------------------ |
| Reader     | View approved articles, subscribed content |
| Journalist | Create and update their own articles       |
| Editor     | Approve and delete articles                |

---

## Publishers

Publishers represent organisations that articles belong to.

* Editors can create publishers
* Journalists assign publishers when creating articles
* Articles are grouped under organisations

---

## API Endpoints

| Method | Endpoint                     | Description                          |
| ------ | ---------------------------- | ------------------------------------ |
| GET    | `/api/articles/`             | List approved articles               |
| GET    | `/api/articles/subscribed/`  | Articles from subscribed journalists |
| GET    | `/api/articles/<id>/`        | Retrieve a single article            |
| POST   | `/api/articles/create/`      | Create article (journalists only)    |
| PUT    | `/api/articles/<id>/update/` | Update article                       |
| DELETE | `/api/articles/<id>/delete/` | Delete article                       |

---

## How to Use the Application

### 1. Register & Login

* Visit: http://127.0.0.1:8000/
* Register as Reader, Journalist, or Editor
* Log in

---

### 2. Create Article (Journalist)

* Create article via UI or API
* Articles start as **unapproved**

---

### 3. Approve Article (Editor)

* Editors approve articles

---

### 4. View Articles (Reader)

* Only approved articles are visible

---

### 5. Subscribe to Journalists

Using Django shell:

```bash
python manage.py shell
```

```python
from news_app.models import User

reader = User.objects.get(username='your_reader')
journalist = User.objects.get(username='your_journalist')

reader.subscribed_journalists.add(journalist)
```

Then access:

```
/api/articles/subscribed/
```

---

## Testing

Run tests:

```bash
python manage.py test
```

Tests include:

* API functionality
* Permissions
* Error handling
* Subscription filtering

---

## Documentation

Sphinx-generated documentation is available in:

```
docs/build/html/index.html
```

---

## Security Notes

* Do NOT commit `.env` or credentials
* Use environment variables for all sensitive data
* Keep secrets out of version control

---

## Author

Nicholas Dionissiou