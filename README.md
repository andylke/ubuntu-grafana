# ubuntu-grafana

Install Grafana

```
sudo apt install -y apt-transport-https software-properties-common wget
```

Add this repository for stable releases
```
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
```

Add stable release to source
```
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

```
sudo apt-get update
```
Install
```
sudo apt-get install grafana -y
```

Start and check service status
```
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```
Enable service to start automatically during system startup
```
sudo systemctl enable grafana-server
```

Configure domain and root url to run behind a proxy
```
sudo nano /etc/grafana/grafana.ini
```

```
[server]
domain = localhost
port = 3000
root_url = %(protocol)s://%(domain)s:%(http_port)s/grafana/ 
serve_from_sub_path = true
```
Restart
```
sudo systemctl restart grafana-server
```

Configure Nginx reverse proxy
```
sudo nano /etc/nginx/sites-enabled/default
```
Append the following location config in server listening to 443
```
    location /grafana/ {  
        proxy_pass http://localhost:3000/grafana/;
        proxy_set_header Host                 $http_host; 
        proxy_set_header X-Real-IP            $remote_addr; 
        proxy_set_header X-Forwarded-For      $proxy_add_x_forwarded_for; 
        proxy_set_header X-Forwarded-Proto    $scheme;
        proxy_set_header X-Nginx-Proxy        true;
    } 

```
Verify syntax
```
sudo nginx -t
```
Restart Nginx service
```
sudo systemctl restart nginx
```
Open Grafana at  https://dev-sandbox/grafana
Login with admin/admin,  change to admin/admin@5354 
