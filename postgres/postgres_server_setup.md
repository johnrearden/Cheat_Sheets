# Server installation on a VPS

### Import the repository signing key:
```sudo apt install curl ca-certificates```

```sudo install -d /usr/share/postgresql-common/pgdg```

```sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc```

### Create the repository configuration file:
```sudo sh -c 'echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'```

### Update the package lists:
```sudo apt update```

### Install the latest version of PostgreSQL:
If you want a specific version, use 'postgresql-16' or similar instead of 'postgresql'

```sudo apt install postgresql-17```

# Server Configuration

### Verify that the postgres service is running
```sudo systemctl status postgresql.service```

### Switch to the default postgres user
```sudo -i -u postgres```

### Log into the postgres shell
```psql```

### Set a strong password for the postgres user
```ALTER USER postgres WITH PASSWORD 'secure_password';```

### Edit the postgres config file
```sudo nano /etc/postgresql/<version>/main/postgresql.conf```

### Set the listen_addresses variable
```listen_addresses = '*'```

### Edit pg_hba.conf
```sudo nano /etc/postgresql/<version>/main/pg_hba.conf```

#### Example to allow all users to connect from a specific subnet
```host    all             all             192.168.1.0/24         md5```

#### Example to allow all users from all IPs (less secure, use with caution)
```host    all             all             0.0.0.0/0              md5```

# Configure firewall
Ideally, set up ufw and allow connections on 5432, and also configure a firewall at the service
provider level.

```sudo ufw allow 5432```

```sudo ufw status```

# Optional: Create a Database and User

### Create a new user:

```CREATE USER myuser WITH PASSWORD 'mypassword';```

### Create a database:

```CREATE DATABASE mydatabase OWNER myuser;```

### Grant privileges:

```GRANT ALL PRIVILEGES ON DATABASE mydatabase TO myuser;```

### Create a db url

```postgresql://{DB_USER}:{DB_PASSWORD}@{DB_HOST}:{DB_PORT}/{db_name}```


