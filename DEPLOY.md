# Deploy ke Ubuntu Server

## Prasyarat di server
```bash
sudo apt update
sudo apt install -y docker.io docker-compose-v2 nginx certbot python3-certbot-nginx
sudo systemctl enable --now docker
sudo usermod -aG docker $USER  # logout/login ulang setelah ini
```

## 1. Clone & jalankan container
```bash
git clone <repo-url> ~/portfolio
cd ~/portfolio
docker compose up -d --build
```

Container berjalan di port **8080** secara internal.

## 2. Konfigurasi Nginx (reverse proxy di host)

Buat file `/etc/nginx/sites-available/portfolio`:
```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Aktifkan:
```bash
sudo ln -s /etc/nginx/sites-available/portfolio /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## 3. HTTPS dengan Certbot
```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Certbot otomatis mengupdate konfigurasi Nginx untuk HTTPS dan setup auto-renewal.

## Update konten
```bash
cd ~/portfolio
git pull
docker compose up -d --build
```
