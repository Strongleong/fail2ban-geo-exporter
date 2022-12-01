# fail2ban-geo-exporter

https://github.com/vdcloudcraft/fail2ban-geo-exporter#readme

# Changes
- Added the ability to receive geo-information about IP from https://ipapi.co/. It takes time to poll the current list when starting the exporter
- Disable grouping
- Changed sqlite database query logic to fail2ban default socket request `/var/run/fail2ban/fail2ban.sock`

Works on Fail2Ban v1.0.2
## Docker example
```bash
docker run -it --rm \
    -v ./conf.yml:/f2b-exporter/conf.yml \
    -v /var/run/fail2ban/fail2ban.sock:/f2b-exporter/fail2ban.sock \
    -p 9332:9332 \
    --name fail2ban-geo-exporter \
    chdmitr/fail2ban-geo-exporter 
```

## Docker-copmose example
```yaml
version: '3.8'

networks:
  monitoring:
    driver: bridge

services:
  f2b_exporter:
    image: chdmitr/fail2ban-geo-exporter
    container_name: f2b-exporter
    volumes:
      - /var/run/fail2ban/fail2ban.sock:/f2b-exporter/fail2ban.sock
      - /path/GeoLite2-City.mmdb:/f2b-exporter/db/GeoLite2-City.mmdb:ro
      - /path/f2b.yml:/f2b-exporter/conf.yml
    restart: unless-stopped
    networks:
      - monitoring
```

## Example config for IPApi
```yaml
server:
  listen_address: 0.0.0.0
  port: 9332
geo:
  enabled: True
  provider: IPApi
#f2b: # Optional
#  socket_path: /f2b-exporter/fail2ban.sock # Optional, default value
```

## Example config for MaxmindDB
```yaml
server:
  listen_address: 0.0.0.0
  port: 9332
geo:
  enabled: true
  provider: MaxmindDB
  maxmind:
    db_path: /f2b-exporter/db/GeoLite2-City.mmdb
    ## Uncomment the following section to set default values for IPs that are not in the database
    ## Otherwise entry will be discarded
    # on_error:
    #   city: Error
    #   latitude: 0
    #   longitude: 0
```
