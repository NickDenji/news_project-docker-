# Django News Application

## Overview

This is a Django-based news application that allows **journalists to create articles**, **editors to approve them**, and **readers to view approved content**.

The project demonstrates:

* Role-based access control
* REST API design using Django REST Framework
* Containerisation with Docker
* Flexible database configuration for different environments

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
* SQLite (used in Docker)
* MariaDB (optional for local development)
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

### 3. Configure environment variables (IMPORTANT)

This project uses sensitive values (e.g. Django `SECRET_KEY`, email credentials).
These are **not included in the repository** for security reasons.

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

### 4. (Optional) Configure MariaDB database

If using MariaDB locally:

```sql
CREATE DATABASE news_db;

CREATE USER 'news_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON news_db.* TO 'news_user'@'localhost';
FLUSH PRIVILEGES;
```

Update `settings.py`:

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

### 5. Apply migrations

```bash
python manage.py migrate
```

---

### 6. Create a superuser

```bash
python manage.py createsuperuser
```

---

### 7. Run the development server

```bash
python manage.py runserver
```

---

### 8. Access the application

* Main site: http://127.0.0.1:8000/
* Admin panel: http://127.0.0.1:8000/admin/
* API: http://127.0.0.1:8000/api/articles/

---

## Run with Docker

This project can be run entirely using Docker without additional setup.

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

* SQLite is used automatically inside Docker for simplicity
* MariaDB is optional and used only for local development
* No manual database setup is required when using Docker

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
* This allows grouping of content by organisation

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
* Log in with your credentials

---

### 2. Create an Article (Journalist)

* Log in as a journalist
* Create an article via UI or API
* Articles are initially **unapproved**

---

### 3. Approve an Article (Editor)

* Log in as an editor
* Approve articles from the interface

---

### 4. View Articles (Reader)

* Log in as a reader
* Only approved articles are visible

---

### 5. Subscribe to a Journalist

Use Django shell:

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

Run tests with:

```bash
python manage.py test
```

Tests include:

* Successful API requests
* Permission validation
* Error handling
* Subscription filtering

---

## Documentation

Project documentation is generated using Sphinx and can be found in:

```
docs/build/html/index.html
```

---

## Security Notes

* Do NOT commit `.env` or sensitive credentials
* Ensure all secrets are stored locally
* Use environment variables for configuration

---

## Author

Nicholas Dionissiou
