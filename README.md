nginx-proxy sets up a container running nginx and [docker-gen][1].  docker-gen generate reverse proxy configs for nginx and reloads nginx when containers they are started and stopped.

See [Automated Nginx Reverse Proxy for Docker][2] for why you might want to use this.

I added a VOLUME (/ssl) to place the certificates and key files for SSL. Create one folder named {{host}} for each host and put the server.crt and server.key files inside.

### Usage

To run it:

    $ docker run -d -p 80:80 -p 443:443 -v /var/run/docker.sock:/tmp/docker.sock -v /local/ssl:/ssl atuhut/nginx-proxy-ssl

Then start any containers you want proxied with an env var VIRTUAL_HOST=subdomain.youdomain.com

    $ docker run -e VIRTUAL_HOST=foo.bar.com  ...

    $ docker run -e VIRTUAL_HOST=foo.bar.com -e SSL=TRUE  ... (for SSL support)

Provided your DNS is setup to forward foo.bar.com to the a host running nginx-proxy, the request will be routed to a container with the VIRTUAL_HOST env var set.

### Multiple Ports

If your container exposes multiple ports, nginx-proxy will default to the service running on port 80.  If you need to specify a different port, you can set a VIRTUAL_PORT env var to select a different one.  If your container only exposes one port and it has a VIRTUAL_HOST env var set, that port will be selected.

  [1]: https://github.com/jwilder/docker-gen
  [2]: http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/

### Multiple Hosts

If you need to support multipe virtual hosts for a container, you can separate each enty with commas.  For example, `foo.bar.com,baz.bar.com,bar.com` and each host will be setup the same.
