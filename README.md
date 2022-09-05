# OpenSearch docker

![OpenSearch version](https://img.shields.io/badge/OpenSearch%20version-1.3.0-blue)

Dockerized cluster architecture for OpenSearch with compose.

## Key concepts

- OpenSearch = Elasticsearch
- OpenSearch Dashboards = Kibana

## Setup

OpenSearch images use amazonlinux:2 as the base image. If you run Docker locally, set Docker to use at least 4 GB of RAM in *Preferences* > *Resources*.

Generate the certificates for the cluster:

```bash
bash generate-certs.sh
```

Build and start the cluster:

```bash
docker-compose up --build -d
```

Initialize the security plugin:

```bash
bash init-security.sh
```

Test the connection:

```bash
curl -XGET --insecure https://localhost:9200 -u admin:dashboard
```

## Usage

Access OpenSearch Dashboards via [http://localhost:5601](http://localhost:5601)

Default username is `admin` and password is `dashboard`

`internal_users.yml` contains initial users added to the security plugin’s internal user database. To generate a new password, run:

```bash
docker-compose exec opensearch-node1 bash -c "plugins/opensearch-security/tools/hash.sh -p new_password"
```

After adding or replacing passwords in `internal_users.yml` , re-run `init-security.sh` to apply the new passwords.

## Debug

Bash access to container:

```bash
docker exec -it opensearch-node1 /bin/bash
```

To output audit logs to stdout, edit `opensearch.yml` and add the following option:

```
plugins.security.audit.type: debug
```

Or, set the [audit log storage type](https://opensearch.org/docs/latest/security-plugin/audit-logs/storage-types/) to `internal_opensearch`, if you want to write logs to an audit index of the current cluster.

## Clean-up

To stop the cluster, run:

```bash
docker-compose down
```

To stop the cluster and delete all data volumes, run:

```bash
docker-compose down -v
```