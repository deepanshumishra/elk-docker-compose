# ELK Stack Setup with Docker Compose

This project sets up the **ELK Stack** (Elasticsearch, Logstash, and Kibana) using Docker Compose for a fully functional logging and monitoring solution. The setup also includes secure communication between services using SSL certificates and configurations for data pipelines in Logstash.

## Project Structure

- **Docker Compose**: The `docker-compose.yml` file defines the services for Elasticsearch, Kibana, Logstash, and other components.
- **SSL Certificates**: Certificates are generated for secure communication between services.
- **Logstash Pipeline**: The configuration for Logstash to process log files and send them to Elasticsearch.

## Components

### 1. **Elasticsearch**
Elasticsearch is a distributed search and analytics engine. This setup includes a 3-node Elasticsearch cluster with SSL security enabled.

#### Key Configurations:
- **SSL Encryption**: The communication between Elasticsearch nodes is encrypted using certificates.
- **Security**: The setup uses the `xpack.security` feature to enable authentication and authorization for Elasticsearch.

#### Environment Variables:
- `ELASTIC_PASSWORD`: Password for the `elastic` user.
- `KIBANA_PASSWORD`: Password for the `kibana_system` user.
- `CLUSTER_NAME`: Name of the Elasticsearch cluster.
- `LICENSE`: Type of the Elasticsearch license (e.g., `trial`).

### 2. **Kibana**
Kibana provides a web interface for visualizing the data in Elasticsearch. The service is configured to connect securely to Elasticsearch.

#### Key Configurations:
- **Security**: Uses `kibana_system` credentials to connect to Elasticsearch.
- **SSL**: The connection between Kibana and Elasticsearch is encrypted using the generated certificates.

### 3. **Logstash**
Logstash processes log data and sends it to Elasticsearch for storage and analysis. The Logstash pipeline is configured to read logs from CSV files and process them.

#### Key Configurations:
- **Input Configuration**: Reads log files (e.g., `customers-1000.csv`, `people-1000.csv`) from the Logstash pipeline directory.
- **Filters**: Data transformations and parsing can be configured to meet specific needs.
- **Output**: The processed data is sent to Elasticsearch.

#### Logstash Configuration (`logstash.conf`):
```plaintext
input {
  file {
    path => "/usr/share/logstash/pipeline/customers-1000.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
    codec => plain {
      charset => "UTF-8"
    }
  }
  file {
    path => "/usr/share/logstash/pipeline/people-1000.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
    codec => plain {
      charset => "UTF-8"
    }
  }
}

filter {
  # Add filter configurations here
}

output {
  elasticsearch {
    hosts => ["https://es01:9200"]
    user => "elastic"
    password => "${ELASTIC_PASSWORD}"
    ssl => true
    ssl_certificate_verification => false
  }
}
