# piwireholik
Docker compose setup to run PiHole + Unbound DNS + WireGuard (UI) + DDNS (optional) + PiAlert (optional) behind a Traefik Proxy.

## Basic Information
piwireholik aims to offer a secure and convenient private network, with access from anywhere with VPN and all managed from web interfaces are HTTPS active behind a reverse proxy.

This project is based on [wirehole](https://github.com/IAmStoxe/wirehole), but aims to add more functionality to it and eliminate certain discrepancies that exist in its documentation and example files.

## Contributing
All contributions, issues, suggestions, pull requests... are welcome and appreciated.

## Set up

### Installation
1. Clone the repo.
```bash
git clone https://github.com/M4RC0Sx/piwireholik.git
```

2. Generate a production env file.
```bash
cd piwireholik
cp .env.example .env
```

3. Edit the values of the `.env` file that are between `<>`. Ignore the *NOT USED* sections.

4. Continue reading this section to learn how to obtain certain variables needed for .env.

5. Once the .env is fully configured, start the docker compose stack.
```bash
docker compose up -d
```
If you want to disable a (non-essential) service, just comment it out in the docker compose file.

### First execution
In the first run, you will need to publish a different port 80 (which is in use by Traefik) for the PiHole web interface. You will need to access this interface to configure the private/internal DNS entries to access the other services and the PiHole panel itself without exposing the container ports, all through Traefik.

### Setting up DuckDNS
DuckDNS is a free DynDNS service that will allow you to connect to your VPN from anywhere, even if the IP provided by your ISP is dynamic.

To get **SUBDOMAINS** and **TOKEN** values, go to [the official DuckDNS website](https://www.duckdns.org), log in and register a domain to get your token.

### Setting up Treafik

#### Setting UP HTTPS DNS challenge - ACME
1. Make sure you have your domain DNS configured with Cloudflare.

2. Go to My Profile > API tokens > Global API Key and use this value for the **CF_API_KEY** environment variable.

3. The **CF_API_EMAIL** environment variable will be your Cloudflare email.

### Setting up Traefik network
Traefik uses a Docker network to discover services. This network is declared as `external` in the docker-compose file. You must create it manually:
```bash
docker network create traefik_network 
```

#### Setting up Traefik dashboard password
1. Install the `apache2-utils` package.
```bash
sudo apt install apache2-utils
```

2. Generate the password.
```bash
htpasswd -nb <username> <password>
```

3. You should get an output like `username:$asdasd$asdasdasdsas$asdasdqas`.

4. The variable **TRAEFIK_USER** will be `username` and the variable **TRAEFIK_PASSWORD** will be `$asdasd$asdasdasdsas$asdasdqas`.

5. **DO NOT REMOVE THE `'` BETWEEN TRAEFIK_PASSWORD ON THE ENV FILE.**


## Services Overview
### DuckDNS
DuckDNS is a free DynDNS service that provides us with a domain name that will be updated as our IP changes (in case the one provided by our ISP is dynamic).

### Unbound
Unbound is a recursive DNS server that improves our privacy and becomes faster than a conventional DNS server over time.

### PiHole
PiHole is a network-wide ad blocker that also has its own internal DNS server, which will allow us to reach our services through Traefik.

**Once configured in PiHole's internal DNS, your web interface will be available at pihole.local.yourdomain.com.**

### PiAlert
PiAlert is a monitoring service for devices connected to our network. It has several scanning modes and a notification system.

**Once configured in PiHole's internal DNS, your web interface will be available at pialert.local.yourdomain.com.**

### WG-Easy
WG-Easy is a Wireguard project that has an administration interface that will allow us to see the status of our customers in real time, as well as their administration in a comfortable way. With this VPN service we will be able to access our network from outside the home.

**Once configured in PiHole's internal DNS, your web interface will be available at wireguard.local.yourdomain.com.**

### Traefik
Traefik is a reverse proxy server that works through Docker and will allow us to access our services with HTTPS.

We provide a version of the docker compose file without Traefik, whereby you will access each service through its dedicated port.

**Once configured in PiHole's internal DNS, your web interface will be available at traefik.local.yourdomain.com.**

## Credits
Credits to the authors of the wirehole project and to the maintainers of all Docker images used.
