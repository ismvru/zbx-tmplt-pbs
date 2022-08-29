# Zabbix template for Proxmox Backup Server monitoring

- [Zabbix template for Proxmox Backup Server monitoring](#zabbix-template-for-proxmox-backup-server-monitoring)
  - [Macros](#macros)
  - [Items](#items)
  - [Triggers](#triggers)
  - [Autodiscovery](#autodiscovery)
    - [Item prototypes](#item-prototypes)
    - [Trigger prototypes](#trigger-prototypes)
  - [Usage](#usage)

Tested on Zabbix 6.0.7 and Proxmox Backup Server 2.2

## Macros

| Macro                          | Default value                          | Description                                                                                                |
| ------------------------------ | -------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `{$PBS.NODE.NAME}`             | `pbs`                                  | Node name of PBS. Needed system info (CPU/RAM, etc of PBS server)                                          |
| `{$PBS.TOKEN.ID}`              | `USER@REALM!TOKENID`                   | API tokens allow stateless access to most parts of the REST API by another system, software or API client. |
| `{$PBS.TOKEN.SECRET}`          | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` | Secret key.                                                                                                |
| `{$PBS.URL.PORT}`              | `8007`                                 | The API uses the HTTPS protocol and the server listens to port 8007 by default.                            |
| `{$PBS.CPU.PUSE.MAX.WARN}`     | `90`                                   | Maximum used CPU in percentage.                                                                            |
| `{$PBS.MEMORY.PUSE.MAX.WARN}`  | `90`                                   | Maximum used memory in percentage.                                                                         |
| `{$PBS.SWAP.PUSE.MAX.WARN}`    | `90`                                   | Maximum used swap in percentage.                                                                           |
| `{$PBS.ROOT.PUSE.MAX.WARN}`    | `90`                                   | Maximum used root space in percentage.                                                                     |
| `{$PBS.STORAGE.PUSE.MAX.WARN}` | `90`                                   | Maximum used storage space in percentage.                                                                  |

## Items

| Name                                     | Key                          | Type                               | Description                |
| ---------------------------------------- | ---------------------------- | ---------------------------------- | -------------------------- |
| PBS: API Ping                            | `pbs.ping`                   | HTTP agent                         | Api availability           |
| PBS: Datastores usage                    | `pbs.status.datastore-usage` | HTTP agent                         | Datasource info and status |
| PBS: Node status                         | `pbs.node.status`            | HTTP agent                         | Node info and status       |
| PBS: Version                             | `pbs.version`                | HTTP agent                         | PBS Version                |
| PBS: Node status: PBS Node: cpu usage    | `pbs.node.cpu`               | Dependent (from `pbs.node.status`) | Node CPU Usage in percent  |
| PBS: Node status: PBS Node: kernel       | `pbs.node.kversion`          | Dependent (from `pbs.node.status`) | Node kernel version        |
| PBS: Node status: PBS Node: kernel       | `pbs.node.kversion`          | Dependent (from `pbs.node.status`) | Node kernel version        |
| PBS: Node status: PBS Node: memory       | `pbs.node.memory`            | Dependent (from `pbs.node.status`) | Node memory info           |
| PBS Node: memory: PBS Node: memory free  | `pbs.node.memory.free`       | Dependent (from `pbs.node.memory`) | Free memory in bytes       |
| PBS Node: memory: PBS Node: memory total | `pbs.node.memory.total`      | Dependent (from `pbs.node.memory`) | Total memory in bytes      |
| PBS Node: memory: PBS Node: memory used  | `pbs.node.memory.used`       | Dependent (from `pbs.node.memory`) | Used memory in bytes       |
| PBS Node: memory used percent            | `pbs.node.memory.pused`      | Calculated                         | Used memory in percent     |
| PBS: Node status: PBS Node: swap         | `pbs.node.swap`              | Dependent (from `pbs.node.status`) | Node swap info             |
| PBS Node: swap: PBS Node: swap free      | `pbs.node.swap.free`         | Dependent (from `pbs.node.swap`)   | Free swap in bytes         |
| PBS Node: swap: PBS Node: swap total     | `pbs.node.swap.total`        | Dependent (from `pbs.node.swap`)   | Total swap in bytes        |
| PBS Node: swap: PBS Node: swap used      | `pbs.node.swap.used`         | Dependent (from `pbs.node.swap`)   | Used swap in bytes         |
| PBS Node: swap used percent              | `pbs.node.swap.pused`        | Calculated                         | Used swap in percent       |
| PBS: Node status: PBS Node: root         | `pbs.node.root`              | Dependent (from `pbs.node.status`) | Node root info             |
| PBS Node: root: PBS Node: root avail     | `pbs.node.root.avail`        | Dependent (from `pbs.node.root`)   | Free root in bytes         |
| PBS Node: root: PBS Node: root total     | `pbs.node.root.total`        | Dependent (from `pbs.node.root`)   | Total root in bytes        |
| PBS Node: root: PBS Node: root used      | `pbs.node.root.used`         | Dependent (from `pbs.node.root`)   | Used root in bytes         |
| PBS Node: root used percent              | `pbs.node.root.pused`        | Calculated                         | Used root in percent       |
| PBS: Node status: PBS Node: uptime       | `pbs.node.uptime`            | Dependent (from `pbs.node.status`) | Node uptime in seconds     |

## Triggers

| Name                           | Severity | Description                                           |
| ------------------------------ | -------- | ----------------------------------------------------- |
| PBS: API service not available | High     |
| PBS: Node high cpu usage       | Average  | High cpu usage (> `{$PBS.STORAGE.PUSE.MAX.WARN}` %)   |
| PBS: Node high memory usage    | Average  | High memory usage (> `{$PBS.MEMORY.PUSE.MAX.WARN}` %) |
| PBS: Node high swap usage      | Average  | High swap usage (> `{$PBS.SWAP.PUSE.MAX.WARN}` %)     |
| PBS: Node high root usage      | Average  | High root usage (> `{$PBS.ROOT.PUSE.MAX.WARN}` %)     |

## Autodiscovery

### Item prototypes

| Name                                                           | Key                                 | Type                                           | Description                |
| -------------------------------------------------------------- | ----------------------------------- | ---------------------------------------------- | -------------------------- |
| PBS: Datastores usage: PBS Datastore: {#DATASTORE}             | `pbs.datastore[{#DATASTORE}]`       | Dependent (from `pbs.status.datastore-usage`)  | Datasource info and status |
| PBS Datastore: {#DATASTORE}: PBS Datastore: {#DATASTORE} avail | `pbs.datastore.avail[{#DATASTORE}]` | Dependent (from `pbs.datastore[{#DATASTORE}]`) | Free space in bytes        |
| PBS Datastore: {#DATASTORE}: PBS Datastore: {#DATASTORE} total | `pbs.datastore.total[{#DATASTORE}]` | Dependent (from `pbs.datastore[{#DATASTORE}]`) | Total space in bytes       |
| PBS Datastore: {#DATASTORE}: PBS Datastore: {#DATASTORE} used  | `pbs.datastore.used[{#DATASTORE}]`  | Dependent (from `pbs.datastore[{#DATASTORE}]`) | Used space in bytes        |
| PBS Datastore: {#DATASTORE} used percent                       | `pbs.datastore.pused[{#DATASTORE}]` | Calculated                                     | Used space in percent      |

### Trigger prototypes

| Name                                   | Severity | Description                                               |
| -------------------------------------- | -------- | --------------------------------------------------------- |
| PBS Datastore: {#DATASTORE} high usage | Average  | High datastore usage (> `{$PBS.STORAGE.PUSE.MAX.WARN}` %) |

## Usage

- Issue new token in Configuraion -> Access Control -> API Token
- Add API Token permission `Admin` to `/` in Configuraion -> Access Control -> Permissions
- Import `zbx_tmplt_pbs.yaml` to Zabbix
- Attach new template `Proxmox Backup Server by HTTP` to host
- Configure macros:
  - `{$PBS.NODE.NAME}`
  - `{$PBS.TOKEN.ID}`
  - `{$PBS.TOKEN.SECRET}`
