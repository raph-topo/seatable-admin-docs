---
status: new
---

# dtable-db Standalone

On the basis of the previous manual, you can also deploy dtable-db separately.

In the following manual, we will show the steps to setup a three nodes deployment

- A dtable-web node running dtable-web, seaf-server, dtable-events and dtable-storage-server
- A dtable-server node running dtable-server, dtable-storage-server
- A dtable-db node running dtable-db, dtable-storage-server

## Setup dtable-db

### Create docker-compose.yml

The default directory for dtable-db (when installed on a separate node) is `/opt/dtable-db`. Create the directory:

```sh
mkdir /opt/dtable-db
```

vim /opt/dtable-db/docker-compose.yml

```
services:
  dtable-db:
    image: seatable/seatable-enterprise:latest
    container_name: dtable-db
    ports:
      # Only expose dtable-db port
      - "7777:7777"
    volumes:
      - /opt/dtable-db:/shared
      - type: bind
        source: "./seatable-license.txt"
        target: "/shared/seatable/seatable-license.txt"
        read_only: ${SEATABLE_LICENSE_FORCE_READ_ONLY:-false}
    environment:
      # Specifies your host name if https is enabled
      - SEATABLE_SERVER_HOSTNAME=dtable-db.example.com
      - SEATABLE_SERVER_LETSENCRYPT=False
      # Optional, default is UTC. Set to your local time zone.
      - TIME_ZONE=Europe/Berlin
      # Disable all services but dtable-db and dtable-storage-server
      - ENABLE_SEAFILE_SERVER=false
      - ENABLE_DTABLE_WEB=false
      - ENABLE_DTABLE_SERVER=false
      - ENABLE_DTABLE_DB=true
      - ENABLE_DTABLE_STORAGE_SERVER=true
      - ENABLE_DTABLE_EVENTS=false
      - DTABLE_EVENTS_TASK_MODE=all
```

### Start dtable-db

```sh
docker compose up -d
```

When you see following in the output log, it means success:

```
Skip seafile-server
Skip dtable-events
Skip dtable-web
Skip dtable-server

SeaTable started

```

## Modify dtable-web server configuration file

Modify the configuration file : `/Your SeaTable data volume/seatable/conf/seatable-controller.conf`

```sh
ENABLE_DTABLE_DB=false

```

Modify dtable-web configuration file `/Your SeaTable data volume/seatable/conf/dtable_web_settings.py`

```
DTABLE_DB_URL = 'https://dtable-db.example.com'  # dtable-db server's url
INNER_DTABLE_DB_URL = 'http://192.168.0.3'  # LAN dtable-db server's url

```

### Restart dtable-web server

```sh
docker compose up -d

docker exec -it seatable bash

seatable.sh

```

When you see following in the output log, it means success:

```
Skip dtable-server
Skip dtable-db

SeaTable started

```

## Modify dtable-server server configuration file

Modify dtable-server configuration file `/Your SeaTable data volume/seatable/conf/dtable_server_config.json`

```
"dtable_db_service_url":  "https://dtable-db.example.com"  // dtable-db server's url

```

### Restart dtable-server server

```sh
docker compose up -d

docker exec -it seatable bash

seatable.sh

```

When you see following in the output log, it means success:

```
Skip seafile-server
Skip dtable-events
Skip dtable-web
Skip dtable-db

SeaTable started

```
