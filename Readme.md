English Version herafater


# Simulateur d'Équipements Réseau SNMP avec Docker

Ce projet permet de simuler une variété d'équipements réseau exposant des données via SNMP (Simple Network Management Protocol). Chaque équipement simulé est lancé dans un conteneur Docker distinct et obtient sa propre adresse IP sur votre réseau local (LAN) grâce à la technologie `macvlan` de Docker.

Ceci est particulièrement utile pour tester des Systèmes de Gestion de Réseau (NMS), des scripts d'audit, des outils de découverte réseau, ou pour se familiariser avec les MIBs SNMP de différents types d'appareils sans avoir besoin du matériel physique.

## Fonctionnalités

* Simulation de multiples types d'appareils réseau (voir liste ci-dessous).
* Chaque appareil simulé est accessible sur sa propre adresse IP sur le LAN.
* Configuration flexible des détails de chaque appareil (nom, localisation, description, etc.) via des variables d'environnement dans `docker-compose.yml`.
* Basé sur Net-SNMP, un agent SNMP robuste et largement utilisé.
* Facile à étendre pour simuler de nouveaux types d'appareils.

## Appareils Simulés

Actuellement, ce projet fournit des templates pour simuler les types d'appareils suivants :

1.  **Imprimantes Réseau** (2 instances par défaut)
2.  **Switch Réseau** (type Cisco Catalyst)
3.  **Routeur Réseau** (type Juniper MX)
4.  **Serveur Linux** (basé sur Net-SNMP)
5.  **Firewall** (type FortiGate)
6.  **Point d'Accès Wi-Fi (WAP)** (type Cisco Aironet)
7.  **Onduleur (UPS)** (type APC Smart-UPS)
8.  **Serveur de Stockage NAS** (type Synology DiskStation)

## Prérequis

* **Docker Engine**
* **Docker Compose** (généralement inclus avec Docker Desktop ou installable séparément sur Linux)
* Un **environnement réseau LAN** supportant `macvlan`. Une interface réseau **filaire (Ethernet)** sur l'hôte Docker est fortement recommandée. Le Wi-Fi est souvent problématique avec `macvlan`.
* Des **adresses IP disponibles** sur votre LAN pour chaque appareil simulé.
* Une compréhension de base de SNMP (communautés, OIDs, MIBs) est utile.

## Structure du Projet


## Configuration

1.  **Configuration du réseau `macvlan` :**
    Ouvrez le fichier `docker-compose.yml`. Vous **devez** adapter la section `networks: printer_lan_net:` pour qu'elle corresponde à votre propre configuration réseau :
    * `parent: enp3s0` : Remplacez `enp3s0` par le nom de l'interface réseau physique de votre machine hôte connectée à votre LAN (ex: `eno1`, `eth0`). Utilisez `ip addr show` sur votre hôte pour trouver le nom correct.
    * `subnet: 192.168.1.0/24` : Adaptez à votre sous-réseau LAN.
    * `gateway: 192.168.1.1` : Adaptez à votre passerelle LAN.

2.  **Adresses IP des conteneurs :**
    Pour chaque service défini dans `docker-compose.yml` (par exemple, `printer_1`, `network_switch_1`, etc.), ajustez la valeur `ipv4_address:` pour assigner une adresse IP unique et disponible sur votre LAN. Assurez-vous que ces adresses ne sont pas dans la plage dynamique de votre serveur DHCP, ou qu'elles y sont réservées.

3.  **Personnalisation des appareils simulés :**
    Pour chaque service dans `docker-compose.yml`, vous pouvez modifier les variables d'environnement (`environment:`) pour personnaliser les informations SNMP exposées (par exemple, `DEVICE_TYPE`, `SYS_NAME_VAL`, `SYS_LOCATION_VAL`, `SYS_DESCR_VAL`, etc.)..

4.  **Templates SNMP :**
    Chaque conteneur possède le même Dockerfile, néanmoins, pour des raisons de customisation, j'ai préféré les recopier.

## Démarrage

1.  Clonez ce dépôt ou copiez les fichiers dans un répertoire sur votre machine hôte Docker.
2.  Configurez `docker-compose.yml` comme décrit dans la section "Configuration" ci-dessus, en particulier les paramètres réseau `macvlan` et les adresses IP.
3.  Ouvrez un terminal dans le répertoire racine du projet et exécutez :
    ```bash
    docker-compose up -d --build
    ```
    Cela construira l'image Docker (si elle n'existe pas ou si le Dockerfile a changé) et démarrera tous les services de simulation en arrière-plan.

## Tester les Simulateurs

Une fois les conteneurs démarrés, vous pouvez utiliser n'importe quel outil SNMP (par exemple, `snmpwalk`, `snmpget`, ou votre NMS) pour interroger les appareils simulés sur leurs adresses IP respectives et le port UDP 161.

Exemple avec `snmpwalk` (en supposant que `printer_1` a l'IP `192.168.1.201` et la communauté `public_lan1` comme défini dans `docker-compose.yml`) :

```bash
snmpwalk -v2c -c public_lan1 192.168.1.201 system
snmpwalk -v2c -c public_lan1 192.168.1.201 .1.3.6.1.2.1.43 # Printer MIB
```

Pour voir les logs d'un simulateur spécifique (par exemple, network_switch_1) :
```bash
docker-compose logs network_switch_1
```

Pour arrêter tous les simulateurs :
```bash
docker-compose down
```

Contributions et suggestions bienvenues !

In English now :

# SNMP Network Device Simulator with Docker

This project allows you to simulate a variety of network devices exposing data via SNMP (Simple Network Management Protocol). Each simulated device runs in a separate Docker container and gets its own IP address on your local area network (LAN) using Docker's `macvlan` technology.

This is particularly useful for testing Network Management Systems (NMS), audit scripts, network discovery tools, or for familiarizing yourself with SNMP MIBs of different device types without needing the physical hardware.

## Features

* Simulation of multiple types of network devices (see list below).
* Each simulated device is accessible on its own IP address on the LAN.
* Flexible configuration of each device's details (name, location, description, etc.) via environment variables in `docker-compose.yml`.
* Based on Net-SNMP, a robust and widely used SNMP agent.
* Easy to extend to simulate new device types.

## Simulated Devices

Currently, this project provides templates to simulate the following device types:

1.  **Network Printers** (2 instances by default)
2.  **Network Switch** (Cisco Catalyst type)
3.  **Network Router** (Juniper MX type)
4.  **Linux Server** (based on Net-SNMP)
5.  **Firewall** (FortiGate type)
6.  **Wireless Access Point (WAP)** (Cisco Aironet type)
7.  **Uninterruptible Power Supply (UPS)** (APC Smart-UPS type)
8.  **Network Attached Storage (NAS)** (Synology DiskStation type)

## Prerequisites

* **Docker Engine**
* **Docker Compose** (usually included with Docker Desktop or installable separately on Linux)
* A **LAN environment** that supports `macvlan`. A **wired (Ethernet)** network interface on the Docker host is strongly recommended. Wi-Fi is often problematic with `macvlan`.
* **Available IP addresses** on your LAN for each simulated device.
* A basic understanding of SNMP (communities, OIDs, MIBs) is helpful.

## Configuration

1.  **`macvlan` Network Configuration:**
    Open the `docker-compose.yml` file. You **must** adapt the `networks: printer_lan_net:` section to match your own network configuration:
    * `parent: enp3s0`: Replace `enp3s0` with the name of the physical network interface of your host machine connected to your LAN (e.g., `eno1`, `eth0`). Use `ip addr show` on your host to find the correct name.
    * `subnet: 192.168.1.0/24`: Adapt to your LAN subnet.
    * `gateway: 192.168.1.1`: Adapt to your LAN gateway.

2.  **Container IP Addresses:**
    For each service defined in `docker-compose.yml` (e.g., `printer_1`, `network_switch_1`, etc.), adjust the `ipv4_address:` value to assign a unique and available IP address on your LAN. Ensure these addresses are not within your DHCP server's dynamic range or are reserved in it.

3.  **Customizing Simulated Devices:**
    For each service in `docker-compose.yml`, you can modify the environment variables (`environment:`) to customize the exposed SNMP information (e.g., `DEVICE_TYPE`, `SYS_NAME_VAL`, `SYS_LOCATION_VAL`, `SYS_DESCR_VAL`, etc.). These variables are used by `entrypoint.sh` to populate the corresponding `snmpd.<DEVICE_TYPE>.conf.template`.

4.  **SNMP Templates:**
    Each container uses the same Dockerfile. However, for customization, the SNMP configurations are managed via separate template files located in the `templates/` directory. You can modify these templates or add new ones to change the SNMP data exposed by the simulated devices.

## Getting Started

1.  Clone this repository or copy the files into a directory on your Docker host machine.
2.  Ensure the `templates/` directory is correctly populated with the necessary `.conf.template` files.
3.  Configure `docker-compose.yml` as described in the "Configuration" section above, especially the `macvlan` network settings and IP addresses.
4.  Open a terminal in the project's root directory and run:
    ```bash
    docker-compose up -d --build
    ```
    This will build the Docker image (if it doesn't exist or if the Dockerfile has changed) and start all simulation services in the background.

## Testing Simulators

Once the containers are running, you can use any SNMP tool (e.g., `snmpwalk`, `snmpget`, or your NMS) to query the simulated devices on their respective IP addresses and UDP port 161.

Example with `snmpwalk` (assuming `printer_1` has the IP `192.168.1.201` and the community `public_lan1` as defined in `docker-compose.yml`):
```bash
snmpwalk -v2c -c public_lan1 192.168.1.201 system
snmpwalk -v2c -c public_lan1 192.168.1.201 .1.3.6.1.2.1.43 # Printer MIB
```

To view the logs of a specific simulator (e.g., network_switch_1):
```bash
docker-compose logs network_switch_1
```
To stop all simulators:
```bash
docker-compose down
```

Contributions and suggestions are welcome!


