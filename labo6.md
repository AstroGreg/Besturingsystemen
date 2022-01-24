# LABO 6: Monitoring

[uitleg](https://git.uclllabs.be/bs2/labos/monitoring)

```bash
ssh bs2
mkdir labo6
cd labo6
git clone ssh://git@git.uclllabs.be:22345/bs2/labos/monitoring.git
cd monitoring
nano docker-compose.yml
```

```yaml
version: '3.2'
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - 9004:9090
    command:
    - --config.file=/etc/prometheus/prometheus.yml
    volumes:
    - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
    depends_on:
    - node-exporter

  node-exporter:
    image: quay.io/prometheus/node-exporter
    ports:
      - 9005:9100
    volumes:
      - /:/host:ro
    command:
      - --path.rootfs=/host

  grafana:
    image: grafana/grafana
    ports:
      - 9009:3000
    volumes:
      - grafana-storage:/var/lib/grafana

volumes:
  grafana-storage:
```

```bash
docker-compose up
```

[http://193.191.177.8:10685](http://193.191.177.8:10685/)

[http://193.191.177.8:10684](http://193.191.177.8:10684/)

[http://193.191.177.8:10689](http://193.191.177.8:10689/)

- Configuration > Data Sources > Add Data Source > Prometheus
    - url: `http://192.0.2.11:9004`

### OPGAVE

- Create > Dashboard
- Add an empty panel
    - CPU in user-mode
        - `rate(node_cpu_seconds_total[5m])`
        - Time series
    - Entropy bits
        - `node_entropy_available_bits`
        - Gauge
    - Beschikbare Gigabytes aan geheugen
        - `node_memory_MemAvailable_bytes/1073741824`
        - Stat
    - Aantal files op de schijf
        - `node_filesystem_files{mountpoint="/"}`
        - Stat
    - Aantal actieve (runnende processen)
        - `node_procs_running`
        - Time series