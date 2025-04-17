# 🔗 Connect AWS RDS PostgreSQL to a Django Project

A step-by-step guide to connect an AWS RDS PostgreSQL database to a Django project, complete with examples.

---

## ✅ Step 1: Launch an AWS RDS PostgreSQL Instance

1. Go to [AWS RDS Console](https://console.aws.amazon.com/rds)
2. Click **"Create database"**
3. Choose:
   - **Engine**: PostgreSQL
   - **Template**: Free tier (if you're testing)
4. Set DB settings:
   - **DB instance identifier**: `mydb`
   - **Master username**: `admin`
   - **Master password**: `mypassword`
5. Under **Connectivity**:
   - Enable public access (if for dev/testing)
   - Choose or create a VPC security group that allows inbound traffic on port `5432`
6. Under **Addtional Configuration**:
   - ** Initial Database Name**: `my_database_name`
7. Click **"Create database"**
8. After creation, copy the **endpoint** of the RDS instance  
   (e.g., `mydb.xxxxxx.us-east-1.rds.amazonaws.com`)

---

## ✅ Step 2: Allow Your IP Address in the RDS Security Group

1. Go to **EC2 > Security Groups**
2. Find the security group used for the RDS instance
3. Go to **Inbound Rules > Edit**
4. Add a rule:
   - **Type**: PostgreSQL
   - **Port**: 5432
   - **Source**: `My IP` or `0.0.0.0/0` (⚠️ less secure — use only for testing)

---

## ✅ Step 3: Install PostgreSQL Driver for Django

```bash
pip install psycopg2-binary
```

---

## ✅ Step 4: Update `settings.py`

Modify the `DATABASES` section in your Django project:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydbname',  # replace with your DB name
        'USER': 'admin',     # your RDS master username
        'PASSWORD': 'mypassword',  # your RDS password
        'HOST': 'mydb.xxxxxx.us-east-1.rds.amazonaws.com',  # your RDS endpoint
        'PORT': '5432',
    }
}
```

---

## ✅ Step 5: Run Migrations and Test Connection

```bash
python manage.py makemigrations
python manage.py migrate
```

If successful, Django is connected to your RDS PostgreSQL instance.

To test:

```bash
python manage.py dbshell
```

If you see a PostgreSQL shell, the connection works! ✅

---

## ✅ Step 6 (Optional): Use `.env` for Sensitive Data

1. Install `python-decouple`:

```bash
pip install python-decouple
```

2. Create a `.env` file:

```ini
DB_NAME=mydbname
DB_USER=admin
DB_PASSWORD=mypassword
DB_HOST=mydb.xxxxxx.us-east-1.rds.amazonaws.com
DB_PORT=5432
```

3. Update `settings.py`:

```python
from decouple import config

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST'),
        'PORT': config('DB_PORT'),
    }
}
```

---

## ✅ Bonus Tips

- Using **Docker**? Make sure your container can access the RDS endpoint (e.g., `network_mode: "host"` or setup VPC peering for private access).
- **Never expose RDS publicly in production**:
  - Use private subnets
  - Add a bastion host
  - Use AWS Secrets Manager for credentials
