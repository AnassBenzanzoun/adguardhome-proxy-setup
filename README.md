# AdGuard Home with Nginx Proxy Setup

This setup uses Docker Compose to run AdGuard Home and an Nginx proxy in separate containers on the same custom network.
I did it because i need all my home devices protected from ads.. better solution would be to buy a raspberry pi and set it up but it cost way too much this days


## Services

### AdGuard Home (`adguard`)

AdGuard Home is a network-wide software for blocking ads & tracking. It operates as a DNS server that re-routes tracking domains to a "black hole," thus preventing your devices from connecting to those servers.

The AdGuard Home container uses the `adguard/adguardhome` image. It has two volumes mounted from the host machine to persist data and configuration:

- `./data:/opt/adguardhome/work`: This volume stores AdGuard Home's working files, such as filters and statistics.
- `./conf:/opt/adguardhome/conf`: This volume stores AdGuard Home's configuration files.

The `restart: unless-stopped` option ensures that the AdGuard Home container automatically restarts if it crashes or if the host machine is rebooted, unless it was manually stopped.

### Nginx Proxy (`nginx-proxy`)

The Nginx proxy is used to forward DNS requests to AdGuard Home. It listens on port 53 and forwards the requests to the AdGuard Home container.

The Nginx proxy container uses the `nginx:latest` image. It's also part of the same custom network as the AdGuard Home container, which allows it to communicate with the AdGuard Home container.

The `ports` section of the `nginx-proxy` service in the Docker Compose file needs to be updated to expose port 53 for both TCP and UDP traffic, as well as any other ports that Nginx should listen on.

## Network

Both services are part of a custom network (`custom_network`). This allows the containers to communicate with each other. The network is created with the `bridge` driver, which creates a private internal network on the host machine.

## Usage

To start the services, run `docker-compose up -d` in the directory containing the `docker-compose.yml` file. This will start the containers in the background.

To stop the services, run `docker-compose down` in the same directory.

## first boot
at first boot change nginx file to forward on port 3000 because that's where the initial adguardhome ui runs.. when the setup ends you can put it back to 80