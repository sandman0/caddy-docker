# Caddy

A [Docker](http://docker.com) image for [Caddy](http://caddyserver.com). This image includes the [git](https://caddyserver.com/docs/http.git), [http.filter](https://caddyserver.com/docs/http.filter) and [namecheap](https://caddyserver.com/docs/tls.dns.namecheap) plugins.  Plugins can also be configured via the `plugins` build arg.

### Credit

A big thank you to [adriel](https://github.com/adriel/caddy-docker) whos this image is forked off.

### Telemetry
Telemetry has been disabled by default on this image!

### Getting Started

```sh
$ docker run -d -p 2015:2015 adriel/caddy
```

Point your browser to `http://127.0.0.1:2015`.

> Be aware! If you don't bind mount the location certificates are saved to, you may hit Let's Encrypt rate [limits](https://letsencrypt.org/docs/rate-limits/) rending further certificate generation or renewal disallowed (for a fixed period)! See "Saving Certificates" below!

### Saving Certificates

Save certificates on host machine to prevent regeneration every time container starts.
Let's Encrypt has [rate limit](https://community.letsencrypt.org/t/rate-limits-for-lets-encrypt/6769).
```sh
$ docker run -d \
    -v $(pwd)/Caddyfile:/etc/Caddyfile \
    -v $HOME/.caddy:/root/.caddy \
    -p 80:80 -p 443:443 \
    sandman0/caddy
```


Here, `/root/.caddy` is the location *inside* the container where Caddy will save certificates.

Additionally, you can use an *environment variable* to define the exact location Caddy should save generated certificates:

```sh
$ docker run -d \
    -e "CADDYPATH=/etc/caddycerts" \
    -v $HOME/.caddy:/etc/caddycerts \
    -p 80:80 -p 443:443 \
    sandman0/caddy
```

Above, we utilize the `CADDYPATH` environment variable to define a different location inside the container for
certificates to be stored. This is probably the safest option as it ensures any future docker image changes don't interfere with your ability to save certificates!

### Using Namecheap

Caddy can talk to Namecheap via their API to automaticly confugure/update your Let's Encrypt certificates using the Namecheap plugin (which has been included in this image).

Follow the [instrutions here](https://caddyserver.com/docs/automatic-https#enabling-the-dns-challenge) to set it up.

### Using git sources

Caddy can serve sites from git repository using [git](https://caddyserver.com/docs/git) plugin.

##### Create Caddyfile

Replace `github.com/adriel/webtest` with your repository.

```sh
$ printf "0.0.0.0\nroot src\ngit github.com/adriel/webtest" > Caddyfile
```

##### Run the image

```sh
$ docker run -d -v $(pwd)/Caddyfile:/etc/Caddyfile -p 2015:2015 sandman0/caddy
```
Point your browser to `http://127.0.0.1:2015`.

## Usage

#### Default Caddyfile

The image contains a default Caddyfile.

```
0.0.0.0
browse
fastcgi / 127.0.0.1:9000 php # php variant only
```
The last 2 lines are only present in the php variant.

#### Paths in container

Caddyfile: `/etc/Caddyfile`

Sites root: `/srv`

#### Using local Caddyfile and sites root

Replace `/path/to/Caddyfile` and `/path/to/sites/root` accordingly.

```sh
$ docker run -d \
    -v /path/to/sites/root:/srv \
    -v path/to/Caddyfile:/etc/Caddyfile \
    -p 2015:2015 \
    sandman0/caddy
```

### Let's Encrypt Auto SSL
**Note** that this does not work on local environments.

Use a valid domain and add email to your Caddyfile to avoid prompt at runtime.
Replace `mydomain.com` with your domain and `user@host.com` with your email.
```
mydomain.com
tls user@host.com
```

##### Run the image

You can change the the ports if ports 80 and 443 are not available on host. e.g. 81:80, 444:443

```sh
$ docker run -d \
    -v $(pwd)/Caddyfile:/etc/Caddyfile \
    -p 80:80 -p 443:443 \
    sandman0/caddy
```
