# Auto updating the nodebalancers ssl cert

Here's a link to a nice docker implementation: 
[ianepperson github](https://github.com/ianepperson/lets-encrypt-linode/blob/master/install_cert.sh)

[link to community.letsencrypt](https://community.letsencrypt.org/t/how-to-add-a-post-auto-renew-hook-for-a-single-certificate/123607/4)

### Steps
- Create a linode API key
- Run ```linode-cli configure --token```
- ```linode-cli nodebalancers list``` to get the NodeBalancer id (store as env var)
- ```linode-cli nodebalancers config-list $NODEBALANCER_ID``` to get the 443/ssl config id

### Script to push cert and key to NodeBalancer

[Linode CLI docs](https://techdocs.akamai.com/linode-api/reference/post-node-balancer)

[Linode CLI raw API listing](https://raw.githubusercontent.com/linode/linode-api-docs/refs/heads/development/openapi.json)


First copy the symlink to a folder that the linode-cli has access to.
<pre>
sudo cp /etc/letsencrypt/live/pregcheck.ai/fullchain.pem ~/ssl/fullchain.pem
</pre>

<pre>
sudo cp /etc/letsencrypt/live/pregcheck.ai/privkey.pem  ~/ssl/privkey.pem
</pre>

<pre>
linode-cli nodebalancers config-update $NODEBALANCER_ID $NODEBALANCER_HTTPS_CONFIG_ID --ssl_cert ~/ssl/fullchain.pem --ssl_key ~/ssl/privkey.pem --no-defaults
</pre>