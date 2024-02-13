# Postgres database

> Some remarks ...  

## Installation

> Some remarks about installation process ...

## Authentication

- **Default credentials**
  - host: localhost
  - port: 5432
  - user: postgres
  - pass: postgres  

  ```bash
  "postgresql://user:pass@host:5432/database"
  ```

> **to allow remote access to the default postgres user do:**  
> `sudo -u postgres psql postgres`  
> then  
> `\du`
