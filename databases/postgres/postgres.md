# Postgres database

> Some remarks ...  

## Installation

> Some remarks about installation process ...  
> [How to login and authenticate to Postgresql after a fresh install?](https://stackoverflow.com/questions/2172569/how-to-login-and-authenticate-to-postgresql-after-a-fresh-install)

## Authentication

- **Default credentials**
  - host: localhost
  - port: 5432
  - user: postgres
  - pass: postgres  

  ```bash
  "postgresql://user:pass@host:5432/database"
  ```

- **Allow remote access to the default postgres user do:**  
`sudo -u postgres psql postgres`  
then

  ```bash
  \du or \du+
  \password postgres
  ```

  - Enter new password for user "postgres": postgres  
  - Enter it again: postgres  
  - Ready!
