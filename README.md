## Script auto setup domain
#### Please read the full content. 
```
#!/bin/bash;
DOMAIN="dangtrinh.name.vn" # thay bằng domain của bạn
PORT="3000" # thay bằng port app của bạn

echo "=== Update system & install Nginx + Certbot ==="
sudo apt update
sudo apt install -y nginx certbot python3-certbot-nginx

echo "=== Create Nginx config for $DOMAIN ==="
NGINX_CONF="/etc/nginx/sites-available/$DOMAIN"

sudo tee $NGINX_CONF > /dev/null <<EOL
server {
    listen 80;
    server_name $DOMAIN;

    location / {
        proxy_pass http://localhost:$PORT;
        proxy_http_version 1.1;
        proxy_set_header Upgrade \$http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host \$host;
        proxy_cache_bypass \$http_upgrade;
    }
}
EOL

echo "=== Enable site ==="
sudo ln -s /etc/nginx/sites-available/$DOMAIN /etc/nginx/sites-enabled/ || true

echo "=== Test & reload Nginx ==="
sudo nginx -t && sudo systemctl reload nginx

echo "=== Obtain SSL certificate for $DOMAIN ==="
sudo certbot --nginx -d $DOMAIN --non-interactive --agree-tos -m admin@$DOMAIN

echo "=== Done! Site is live at: https://$DOMAIN ==="
```

#### How to run scrip
```
nano setup-nginx.sh
chmod +x setup-nginx.sh
./setup-nginx.sh
```
