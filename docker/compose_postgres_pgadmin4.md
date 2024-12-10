# Docker compose for postgresql and pgadmin4
## compose file
```yaml
name: dbcompose
services:
  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: pgadmin
    environment:
      - PGADMIN_DEFAULT_EMAIL=admin@admin.com
      - PGADMIN_DEFAULT_PASSWORD=admin
    ports:
      - "5000:80"
    depends_on:
      - postgres

  postgres:
    image: postgres:latest
    container_name: postgres
    environment:
      - POSTGRES_USER=nhan
      - POSTGRES_PASSWORD=nhan
      - POSTGRES_DB=mydb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  pgadmin_data:
    driver: local
  postgres_data:
    driver: local
```

## start container
```cmd
docker compose up -d
```

