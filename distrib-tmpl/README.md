# Hercules Ragnarok Online Server

## Release information

* Hercules release [GIT_VERSION](https://github.com/HerculesWS/Hercules/releases/tag/GIT_VERSION)
* Server mode: SERVER_MODE
* Packet version: PACKET_VER

Currently this is only available for arm32v7 and arm64v8 architectures, for use e.g. on Raspberry Pi minicomputers.


# Usage

Paste the following code block into a file called `docker-compose.yml` in a new directory and run `docker-compose up` to bring up a server.

    version: '3.2'

    services:
        db:
            image: mariadb:10.4
            restart: on-failure
            environment:
                MYSQL_ROOT_PASSWORD: hercroot
                MYSQL_USER: ragnarok
                MYSQL_PASSWORD: ragnarok
                MYSQL_DATABASE: ragnarok
                INTERSERVER_USER: wisp
                INTERSERVER_PASSWORD: wisp
            ports:
                - 3306:3306
            volumes:
                - mysql_data:/var/lib/mysql
                - ./sql-files/SERVER_MODE:/docker-entrypoint-initdb.d

        game_servers:
            image: florianpiesche/hercules-SERVER_MODE-default
            restart: on-failure
            depends_on:
                - db
            ports:
                # login server
                - 6900:6900
                # character server
                - 6121:6121
                # map server
                - 5121:5121
            volumes:
                - configuration:/hercules/conf/import

    volumes:
        mysql_data:
        configuration:

## Editing server configuration

You can edit the server configuration by running `docker exec -it hercules-game_servers_1 /bin/bash` and modifying the configuration files in `/hercules/conf/import`. Changes in the `import` directory will persist even if you rebuild or update the image.

You can also edit the configuration on the host system. To find out where Docker has stored these files on the host, run `docker volume inspect hercules_configuration` and look for the `Mountpoint`.

For configuration changes to take effect, you will need to restart the game server container: `docker restart hercules-game_servers_1`.