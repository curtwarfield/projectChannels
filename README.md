# projectChannels

> **Guide to creating a secure static website.**

## Getting a domain name

The first step in creating your own website is registering a domain name. There are hundreds of domain registars to choose from but I'd recommend going with a well-known company such as [Namecheap](https://www.namecheap.com/) or [Porkbun](https://porkbun.com/).

Free options are available but I they aren't as reliable as **top-level domains(TLD)** such as **.com** or **.org**.

You should be able to find a **.com** domain for under $10 a year.

We'll be using Cloudflare's **Domain Name System (DNS)** services for our web services.

After you've registered your domain name, configure your domain to use Cloudflare's `DNS` services.

Every domain registar's dashboard is unique, so you'll need to find out how you make `DNS` changes for your domain.

## Creating a **Cloudflare** account

1. Go to [Cloudflare](https://cloudflare.com) to create a new acccount or log into your account if you already have one.

2. Add your domain name to your `Cloudflare` account. **Cloudflare** searches for any existing `DNS` records for your domain and gives you the `Nameservers` to use on your registar's dashboard.

3. Add your public IP address as an `A` record in the `DNS` settings. If you don't know your public IP address, go to [whatismyip.org](https://whatismyip.org) to verify it. Make sure you leave the **Proxied** status enabled to hide your public IP address. This makes your website more secure.

4. Add **Cloudflare's** `Nameservers` in your domain registar's dashboard to make sure these are the only `Nameservers` your domain is using.
    > **It can take a few hours or more for these changes to populate across the internet.**

## Creating a **Cloudflare** Argo tunnel

Install the `cloudflared` package

~~~
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && dpkg -i cloudflared-linux-amd64.deb
~~~


