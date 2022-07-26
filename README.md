# projectChannels

> **Guide to creating a secure static website on Ubuntu based Linux distributions.**

## Getting a domain name

The first step in creating your own website is registering a domain name. There are hundreds of domain registars to choose from but I'd recommend going with a well-known company such as [Namecheap](https://www.namecheap.com/) or [Porkbun](https://porkbun.com/). Free options are available but I they aren't as reliable as **top-level domains(TLD)** such as **.com** or **.org**. You can usually find a **.com** domain for under $10 a year.

We'll be using [Cloudflare's](https://cloudflare.com) **Domain Name System (DNS)** services for our web services. After you've registered your domain name, you will configure your domain to use **Cloudflare's** `DNS` services.


## Creating a **Cloudflare** account

1. Go to [Cloudflare](https://cloudflare.com) to create a new acccount or log into your account if you already have one.

2. Add your domain name to your `Cloudflare` account. **Cloudflare** searches for any existing `DNS` records to import for your domain and gives you the `Nameservers` that you need to use for your domain registar.
    > #### Choose the free option when adding your domain!

3. Add your public IP address as an `A` record in the `DNS` settings. If you don't know your public IP address, go to [whatismyip.org](https://whatismyip.org) to verify it. Make sure you leave the **Proxied** status enabled to hide your public IP address. This makes your website more secure.

4. Add **Cloudflare's** `Nameservers` in your domain registar's dashboard to make sure these are the only `Nameservers` your domain is using. Every domain registar's dashboard is unique, so you'll need to find out how to make `DNS` changes for your domain.
    > **It can take a few hours or more for DNS changes to populate globally across the internet.**

## Creating a **Cloudflare** Argo tunnel

Install the `cloudflared` package

~~~
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && dpkg -i cloudflared-linux-amd64.deb
~~~


