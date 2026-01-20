```yaml
version: '3.9'

services:
  minio:
    image: minio/minio:latest
    container_name: minio
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    ports:
      - "9000:9000"      # основной API порт
      - "9001:9001"      # веб-интерфейс
    volumes:
      - minio_data:/data
    command: server /data --console-address ":9001"
    restart: unless-stopped

volumes:
  minio_data:
```

