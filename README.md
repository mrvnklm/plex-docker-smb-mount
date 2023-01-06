# plex-docker-smb-mount
simple solution to mount smb network share in plex docker container

## Usage with Home Assistant OS
We are going to use [linuxserver/plex](https://hub.docker.com/r/linuxserver/plex) as we can not modify the official plex addon docker container. Please make sure to stop or delete the official addon before.
1. clone this repository in your Home Assistant `share` directory to a folder called `plex`
2. open `init-smb-mount\run` with a text editor and update the variables according to your samba server (use the server IP instead of the hostname in order to prevent DNS issues)
3. run the docker container via the offical Home Assistant SSH addon by using the following command:
```
docker run -d \
--name=plex \
--net=host \
--restart unless-stopped \
--privileged \
--label=com.centurylinklabs.watchtower.enable=true \
-e VERSION=docker \
-v /mnt/data/supervisor/share/plex/config:/config \
-v /mnt/data/supervisor/share/plex/init-smb-mount:/etc/s6-overlay/s6-rc.d/init-smb-mount \
-v /mnt/data/supervisor/share/plex/init-smb-mount/init-smb-mount:/etc/s6-overlay/s6-rc.d/user/contents.d/init-smb-mount \
lscr.io/linuxserver/plex:latest
```

## Updating
the plex docker container can be updated or recreated without any problems, for automatic updating consider using [watchtower](https://containrrr.dev/watchtower/)
```
docker run -d \
--name watchtower \
--restart=unless-stopped \
-e WATCHTOWER_CLEANUP=1 \
-e WATCHTOWER_LABEL_ENABLE=1 \
-e WATCHTOWER_SCHEDULE="0 0 4 * * *" \
--label=com.centurylinklabs.watchtower.enable=true \
-v /var/run/docker.sock:/var/run/docker.sock \
containrrr/watchtower
```

## Notes
Home Assistant will detect, that you run external docker containers and "You are running an unsupported installation" will show up. You can just ignore that.
- the `--privileged` is required for mounting 
- using `--label=com.centurylinklabs.watchtower.enable=true` together with `-e WATCHTOWER_LABEL_ENABLE=1` makes sure, that watchtower will not interfere with any of the Home Assistant containers
- checking for updates every night at 04:00 is done by using the following cron expression `-e WATCHTOWER_SCHEDULE="0 0 4 * * *"`

## Usage with other OS
just update the hosts mount points according to your configuration
