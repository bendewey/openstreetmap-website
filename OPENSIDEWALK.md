# Docs

- Read [README.md](README.md), [INSTALL.md](INSTALL.md), and [DOCKER.md](DOCKER.md)

# Install

1. git clone --depth=1 https://github.com/openstreetmap/openstreetmap-website.git
1. Follow from Docker instructions: [DOCKER.md](DOCKER.md)
1. Modify database.yml uncomment username, password, host from all three.  change host to `db` and username password for test and production to openstreetmap
1. run `docker-pull mailhog/mailhog`
1. Change settings.yml (maybe add to settings.local.yml) and add       
    * smtp_address: "mailhog"
    * smtp_port: 1025
1. add mailhog to docker-compose.yml
```
   mailhog:
    image: mailhog/mailhog
    logging:
      driver: 'none'  # disable saving logs
    ports:
      - 1025:1025 # smtp server
      - 8025:8025 # web ui
```
1. add mailhog as a dependency of web in docker-compose  (can this be switched to sendgrid?)
1. update openstreetmap-website/app/assets/javascripts/edit/id.js.erb
 change line 6 to URL
1. run docker-compose build and up from Docker.md
1. Before Migrations run db:create, btree, and functions installs from Install.md using `docker exec db bash`
1.  run db:migrate from Docker.md
1.  instead of wget, run `curl -g https://download.geofabrik.de/north-america/us/district-of-columbia-latest.osm.pbf >> district-of-columbia-latest.osm.pbf`
1.  run docker osmosis
1.  launch http://localhost:3000
1.  register and create an account, check http://localhost:8025 for verification email link.  (is there a setting for the url in the registration email?)