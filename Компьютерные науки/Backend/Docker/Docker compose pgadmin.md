```yaml
services:
  postgres:
    image: postgres:18
    restart: always
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/18/docker
    networks:
      - pg-network

  pgadmin:
    image: dpage/pgadmin4:latest
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: secretpassword
      # Optional: set server mode to False for a single-user local setup
      PGADMIN_CONFIG_SERVER_MODE: 'False' 
    ports:
      - "8080:80"
    networks:
      - pg-network
    depends_on:
      - postgres

volumes:
  postgres_data:

networks:
  pg-network:
    driver: bridge
```