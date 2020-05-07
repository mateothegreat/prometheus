# Prometheus ansible role

This role will install the latest & configure prometheus.

## Variables

```yaml
prometheus:
  version: "2.18.0"
  install_path: "/usr/local/bin"
  config_dir: "/etc/prometheus"
  listen_address: "0.0.0.0:9090"
  tsdb:
    path: "/var/lib/prometheus"
    retention_time: "15d"
    retention_size: "1GB"
  evaluation_interval: "15s"
  user: "prometheus"
  group: "prometheus"
  scrape_interval: "15s"
  scrape_timeout: "10s"
  external_labels:
    environment: "production"
  scrape_configs:
    - job_name: "redis"
      static_configs:
        - targets: ["127.0.0.1:9101"]
    - job_name: "ec2-autoscaling"
      ec2_sd_configs:
        - region: "eu-central-1"
          access_key: "changeme"
          secret_key: "changeme"
          port: 9100
      relabel_configs:
        - source_labels: ["__meta_ec2_tag_Name"]
          regex: "some-instance-prefix-*"
          action: "keep"
        - source_labels: ["__meta_ec2_tag_Name", "__meta_ec2_private_ip"]
          target_label: "instance"
```

## Example Usage

Add the following to a file like `playbook.yaml`:

```yaml
- hosts: monitoring
  roles:
    - role: "mateothegreat.prometheus"
      var:
        prometheus:
          version: "2.18.0"
          install_path: "/usr/local/bin"
          config_dir: "/etc/prometheus"
          listen_address: "0.0.0.0:9090"
          tsdb:
            path: "/var/lib/prometheus"
            retention_time: "15d"
            retention_size: "1GB"
          evaluation_interval: "15s"
          user: "prometheus"
          group: "prometheus"
          scrape_interval: "15s"
          scrape_timeout: "10s"
          external_labels:
            environment: "production"
          scrape_configs:
            - job_name: "redis"
              static_configs:
                - targets: ["127.0.0.1:9101"]
            - job_name: "ec2-autoscaling"
              ec2_sd_configs:
                - region: "eu-central-1"
                  access_key: "changeme"
                  secret_key: "changeme"
                  port: 9100
              relabel_configs:
                - source_labels: ["__meta_ec2_tag_Name"]
                  regex: "some-instance-prefix-*"
                  action: "keep"
                - source_labels: ["__meta_ec2_tag_Name", "__meta_ec2_private_ip"]
                  target_label: "instance"

```

Run with `ansible-playbook -i <your inventory file> playbook.yaml`.
