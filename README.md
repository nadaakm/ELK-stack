# ELK Stack Setup with Docker Compose

This repository contains a Docker Compose file to set up an ELK (Elasticsearch, Logstash, and Kibana) stack, along with Filebeat for log forwarding.

## Prerequisites

- Docker
- Docker Compose

## Services

### 1. Elasticsearch
- **Image**: `elasticsearch:7.5.0`
- **Ports**: `9200:9200`, `9300:9300`
- **Environment Variables**:
  - `bootstrap.memory_lock=true`
  - `discovery.type=single-node`
  - `ES_JAVA_OPTS=-Xms512m -Xmx512m`
- **Container Name**: `elasticsearch`
- **Memory Lock**: Enabled with unlimited soft and hard limits to prevent swapping.

### 2. Kibana
- **Image**: `kibana:7.5.0`
- **Ports**: `5601:5601`
- **Container Name**: `kibana`
- **Dependencies**: Depends on `elasticsearch` to be up and running.

### 3. Logstash
- **Image**: `logstash:7.5.0`
- **Ports**: `5044:5044`
- **Container Name**: `logstash`
- **Volumes**:
  - `./logstash.conf:/usr/share/logstash/pipeline/logstash.conf`
  - `./logstash.template.json:/usr/share/logstash/templates/logstash.template.json`
- **Dependencies**: Depends on `elasticsearch` to be up and running.

### 4. Filebeat
- **Image**: `docker.elastic.co/beats/filebeat:7.5.0`
- **Container Name**: `filebeat`
- **Volumes**:
  - `/var/run/docker.sock:/var/run/docker.sock:ro`
  - `/var/lib/docker/containers:/var/lib/docker/containers:ro`
  - `./filebeat.yml:/usr/share/filebeat/filebeat.yml`
- **Command**: `--strict.perms=false`
- **Dependencies**: Depends on `logstash` to be up and running.
- **Deployment Mode**: Global deployment mode ensures that Filebeat runs on every node in a Swarm cluster (if applicable).

## Usage

1. **Clone the Repository**:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Start the Services**:
   ```bash
   docker-compose up -d
   ```

3. **Access Kibana**:
   Open your browser and go to `http://localhost:5601` to access the Kibana dashboard.

4. **Stopping the Services**:
   ```bash
   docker-compose down
   ```

## Configuration

- **Elasticsearch**: Configured as a single-node cluster with 512MB heap size.
- **Logstash**: Configurations can be adjusted in `logstash.conf`.
- **Filebeat**: The `filebeat.yml` configuration file should be edited to match your log forwarding requirements.

## Volumes

- Mount your custom configuration files into the respective services for custom setups:
  - `logstash.conf` for Logstash pipeline configurations.
  - `filebeat.yml` for Filebeat setup.

## Notes

- Ensure that your system has sufficient memory allocated to Docker, as the ELK stack can be resource-intensive.
- Adjust the memory settings in the `docker-compose.yml` file if necessary.
