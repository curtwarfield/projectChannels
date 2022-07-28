# projectChannels

> **Guide to creating a secure static website on Ubuntu based Linux distributions.**

## Getting a domain name

The first step in creating your own website is registering a domain name. There are hundreds of domain registars to choose from but I'd recommend going with a well-known company such as [Namecheap](https://www.namecheap.com/) or [Porkbun](https://porkbun.com/). Free options are available but I they aren't as reliable as **top-level domains (TLD)** such as **.com** or **.org**. You can usually find a **.com** domain for under $10 a year.

We'll be using [Cloudflare's](https://cloudflare.com) **Domain Name System (DNS)** services for our web services. After you've registered your domain name, you will configure your domain to use **Cloudflare's** `DNS` services.


## Creating a **Cloudflare** account

1. Go to [Cloudflare](https://cloudflare.com) to create a new account or log into your account if you already have one.

2. Add your domain name to your `Cloudflare` account. **Cloudflare** searches for any existing `DNS` records to import for your domain and gives you the `Nameservers` that you need to use for your domain registrar.
    > #### Choose the free option when adding your domain!

3. Add your public IP address as an `A` record in the `DNS` settings. If you don't know your public IP address, go to [whatismyip.org](https://whatismyip.org) to verify it. Make sure you leave the **Proxied** status enabled to hide your public IP address. This makes your website more secure.

4. Add **Cloudflare's** `Nameservers` in your domain registrar's dashboard to make sure these are the only `Nameservers` your domain is using. Every domain registrar's dashboard is unique, so you'll need to find out how to make `DNS` changes for your domain.
    > **It can take a few hours or more for DNS changes to populate globally across the internet.**

## Docker installation and configuration
[Docker](https://www.docker.com) is an application used to create isolated self-sufficient software applications called `containers`. Containers have their own dependencies and configuration. They provide an efficient and stable environment and are an easy way to get applications up and running quickly.

1. Install all of the required `docker` packages.
~~~
sudo apt install docker docker-compose docker-doc docker-registry docker.io
~~~

2. Check to make sure that `docker` is running.
~~~
sudo systemctl status docker
~~~

3. Add yourself to the `docker` group if you don't want to run `docker` as `sudo`.
~~~
sudo usermod -aG docker ${USER}
~~~

> **Substitute the `${USER}` variable for your own name.**

## Installing Nginx Proxy Manager

[Nginx proxy manager](https://nginxproxymanager.com/) **NPM** is a reverse proxy management system that exposes your private network Web services securely with free SSL certificates via [Let's Encrypt](https://letsencrypt.org/). It is designed as a **Docker** `container` and will only use a single `docker-compose.yml` for installation and configuration.

1. To keep things organized, create a docker directory and a npm subdirectory in your home directory.
~~~
mkdir -p docker/npm
~~~

2. From the npm subdirectory create the `docker-compose.yml` configuration file.
~~~
$ cd docker/npm

$ vi docker-compose.yml
~~~
The file needs to contain the following:
~~~
version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "npm"
      DB_MYSQL_NAME: "npm"
    restart: unless-stopped
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
  db:
    image: 'jc21/mariadb-aria:latest'
    environment:
      MYSQL_ROOT_PASSWORD: 'npm'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'npm'
      MYSQL_PASSWORD: 'npm'
    restart: unless-stopped
    volumes:
      - ./data/mysql:/var/lib/mysql
~~~
> **When you are creating your `docker-compose.yml` text file, spacing and indentation is important because it uses [yaml](https://yaml.org/) formatting.**

> **I also recommend changing the passwords from `npm` to something more secure.**

This file looks overwhelming and will be difficult to understand if you don't have any experience with `docker`. But basically here is what the file does.
* Automatically downloads the `Nginx Proxy Manager` application and a `MySQL` database. 
* Creates the storage volumes within your npm subdirectory that the applications will use.
* Specifies the ports that will be used.
* Automatically restart the application if terminated.
* Configures the usernames and passwords.

This is why **Docker** `containers` are so powerful. Everything is self-contained in your npm subdirectory and running one command will automatically launch the application and database for you.

3. Start the `Nginx Proxy Manager` `container`.
~~~
$ docker-compose up -d 
~~~

4. Check the status of the `container`.
~~~
$ docker ps 
~~~
If everything is working successfully, you will see output similar to this:
~~~
IMAGE                     PORTS                                                                                 NAMES
jc21/nginx-proxy-manager  0.0.0.0:80-81->80-81/tcp, :::80-81->80-81/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp  nginx_app_1
jc21/mariadb-aria:latest  3306/tcp                                                                              nginx_db_1
~~~
To make things easier to read, I did not include the entire output.

## Creating a **Cloudflare** Argo tunnel

Install the `cloudflared` package.

~~~
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && dpkg -i cloudflared-linux-amd64.deb
~~~


