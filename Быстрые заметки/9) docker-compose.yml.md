```yml
services:  
  postgres:  
    image: postgres:18.6  
    environment:  
      POSTGRES_USER: "postgres"  
      POSTGRES_PASSWORD: "postgres"  
      POSTGRES_DB: "obsidian_mcp"  
    ports:   
- "5432:5432"  
    healthcheck:  
      test: ["CMD-SHELL", "pg_isready -U postgres -d obsidian_mcp"]  
      interval: 5s  
      timeout: 5s  
      retries: 3  
    restart: unless-stopped
```
