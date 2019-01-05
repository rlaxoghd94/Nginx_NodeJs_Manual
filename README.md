# Nginx Configuration on Mac

### Configuration
The default place of nginx.conf on Mac after installing with brew is:
```
/usr/local/etc/nginx/nginx.conf
```

Changing the default port (8080):
We shall change it to 80. First, stop the nginx server if it is running:
```shell
sudo ngingx -s stop
```
*Note: if apche is running as well, you must also stop apche with `sudo apcachectl stop`*

Then, open `nginx.conf`via an editor
```shell
vim /usr/local/etc/nginx/nginx.conf
```
and change the 8080 to your need:
```
server {
listen       8080;
server_name  localhost;

#access_log  logs/host.access.log  main;

location / {
    root   html;
    index  index.html index.htm;
}
```

Save the configuration and relaunch the nginx via `sudo nginx`

Open a browser and travel to the following URL:
```
http://localhost:{PREFERRED_PORT}
```

### Set up Nginx as a Reverse Proxy server
***A node process must be running and listening on `localhost` for the following steps.***

#### What is a Reverse Proxy?
A reverse proxy is an intermediary proxy service which takes a client request, passes it on to one or more servers, and subsequently delivers the server's response to the client. A common reverse proxy configuring is to put **Nginx in front of a web server**.
Using this method will allow both web servers to work together enabling each to do what they do best.

#### Benefits of an Nginx Reverse Proxy
There a various benefits to setting up an Nginx reverse proxy. Although it's not required in all cases, it can be beneficial depending upon your particular needs / scenario / setup.

- **Load Balancing**: A reverse proxy can perform load balancing which helps distribute client requests evenly across backend servers. This process greatly helps in avoiding the scenario where a particular server becomes overloaded due to a sudden spike in requests. Load balancing also improves redundancy as if one server goes down, the reverse proxy will simply reroute requests to a different server.
- **Increased Security**: A reverse proxy also acts as a line of defense for your backend servers. Configuring a reverse proxy ensures that the identity of your backend servers remains unknown. This can greatly help in protecting your servers from attacks such as *DDoS* for example.
- **Better Performance**: Nginx has been known to perform better in delivering static content over Apache. Therefore, with an Nginx reverse proxy, all client requests can be handled by Nginx, while all requests for dynamic content can be passed on to the backend server. This helps improve performance by ***optimizing the delivery of assets based on their type***. Additionally, reverse proxies can also be used to serve cached content and perform SSL encryption to take a load off the web server(s).
- **Easy Logging and Auditing**: Since there is only one single point of access where a reverse proxy is implemeneted, this makes logging and auditing much simpler. Using this method, you can easily monitor what goes in and out through the reverse proxy.

Nginx configuration is in `/usr/local/etc/nginx/nginx.conf`**(OS X)** file. Open the file for editing with `sudo` privilege.

Within the `server` block, you should have an existing `location /` block. Replace the contents of that block with the following configuration:
```
location / {
        proxy_pass http://localhost:8081;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
}
```
*Note: This example uses 8080 port as the nginx port and 8081 as a preferred port to be redirected upon request*

You can add additional `location` blocks to the same server block to provide access to other applications on the same server. For example, if you were to run another Node.js application on port `8082`, you could add another location block, similar to the one above, but with `8082` port, to allow access to it via `localhost:8080/app2`:

```
location /app2 {
        proxy_pass http://localhost:8082;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
}
```

Check for any syntax errors:
```shell
sudo nginx -t
```
Then, restart Nginx:
```shell
sudo systemctl restart nginx
```

-------------
## Great Practice Examples
To test `Nginx` with its reverse proxy, you need the following:
 - Virtual Machine (VMWare, VirtualBox, etc) network setting set to a `bridged network`
 	- This allows external access from your local host machine to the local virtual machine via an IP Address
	- Must use `sudo ufw allow 80` in order to allow external `HTTP`(port 80) access
	- VM must be equipped with `Nginx`, `Nodejs`, and `npm` (use `npm`to install `express`)
 - Check if `/etc/nginx/nginx.conf` has `:80` open for access
 - Edit `/etc/nginx/sites-available/default`'s `Location /` block
	- add `proxy_pass http://127.0.0.1:{port}`, the address where `Express` is being run at
 - On your local machine, try to access `http://{VM IP Addr}` to check if `Nginx` reroutes to `Express`
 - If all of the above requirements are met, you've just mastered the concept of reverse proxy
 - From here on, you can deepen the complexity of `Nginx` practice by having multiple `Node` application running with different number of *server blocks* within the `nginx.conf` and different number of `worker processes` within the config file

<br></br>
<br></br>
## Node.js manual
- [Link](https://github.com/rlaxoghd94/Nginx_NodeJs_Manual/blob/master/Nodejs/README.md)
