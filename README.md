# Postegres-Db-Monitoring
Monitoring Postgres Database With Docker compose file
## 1. docker compose file for monitoring Postgres with Prometheus and grafana 
```
version: "3.9"
services:  
  grafana:
    image: grafana/grafana
    ports:
      - 3000:3000

  prometheus:
    image: prom/prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro

  postgres:
    image: postgres:15
    ports:
      - 5432:5432
    volumes:
      - ./backup:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: postgrespassword
      POSTGRES_DB: shop

  postgres-exporter:
    image: prometheuscommunity/postgres-exporter
    ports:
      - 9187:9187
    environment:
      DATA_SOURCE_NAME: "postgresql://postgres:postgrespassword@postgres:5432/shop?sslmode=disable"
    links:
      - postgres
      - prometheus
```

## 2. config file for prometheus

```
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: postgres-exporter
    static_configs:
      - targets: ["postgres-exporter:9187"]
```

