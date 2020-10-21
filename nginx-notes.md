# Nginx

## root vs alias:

From: https://www.nginx.com/resources/wiki/start/topics/examples/x-accel/

```bash
# Will serve /var/www/files/myfile.tar.gz
# When passed URI /protected_files/myfile.tar.gz
location /protected_files {
  internal;
  alias /var/www/files;
}

# Will serve /var/www/protected_files/myfile.tar.gz
# When passed URI /protected_files/myfile.tar.gz
location /protected_files {
  internal;
  root /var/www;
}```
