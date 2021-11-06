# Django Server Configs

## Gunicorn

`/etc/systemd/system/gunicorn-<project-name>.service`

```bash
[Unit]
Description=gunicorn <project-name> daemon
After=network.target

[Service]
User=your-username
Group=your-username
WorkingDirectory=/path/to/project/root
ExecStart=/path/to/venv/.../bin/gunicorn \
        --access-logfile /path/to/project/root/logs/gunicorn-access.log \
        # --workers 1\
        # --reload \  # if you want autoreloading gunicorn on code change.
        --bind 127.0.0.1:8080 \
        <project-name>.wsgi:application

[Install]
WantedBy=multi-user.target
```

### Enabling The Gunicorn Service

```bash
sudo systemctl enable gunicorn-<project-name>.service
sudo systemctl start gunicorn-<project-name>
```

## Nginx

```bash
server {
    listen 80;
    server_name subdomain.domain.com;

    location = /favicon.ico { access_log off; log_not_found off; }
    
    location /static/ {
        alias /path/to/project/root/static/;
    }

    location / {
        include proxy_params;
        proxy_set_header X-Forwarded-Proto ssl;
        proxy_headers_hash_max_size 512;
        proxy_headers_hash_bucket_size 128;
        proxy_pass http://127.0.0.1:8080;
    }
}
```


## Celery

`/etc/systemd/system/celery-<project-name>.service`

```bash
[Unit]
Description=celery <project-name> daemon
After=network.target

[Service]
Type=forking
User=your-username
Group=your-username
WorkingDirectory=/path/to/project/root
ExecStart=/bin/sh -c '/path/to/venv/.../bin/celery multi start worker -A <project-name> --logfile=/path/to/project/root/logs/celery.log --loglevel=INFO'

[Install]
WantedBy=multi-user.target
```

### Enabling The Celery Service

```bash
sudo systemctl enable celery-<project-name>.service
sudo systemctl start celery-<project-name>
```

### Celery Extras

Add the following to ExecStart command for various options

* `-Q <project-name>`
  * If you share redis databases with other projects, use a queue name to separate them.
* `--concurrency=2`
  * How many processes to launch?
* `--beat`
  * Do you want to include beat in the worker command?
  * Only run this on a single machine.
