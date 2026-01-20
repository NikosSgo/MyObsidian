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
        
networks:
  pg-network:
    driver: bridge
```