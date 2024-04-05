# media-stack

## VPN

I use Windscribe for my VPN because they aren't hostile toward torrenters and they have a way to port forward. To make it cheap, select two locations and R.O.B.E.R.T. to meet their minimum $3 for a custom built plan. Then upgrade it to port forwarded for $24 for the year.

Port forward `6881` (or whatever you configure below) and download the OpenVPN config. Place the config in `conf/vpn.ovpn`

Here is an example of my `conf/gluetun.env`:

```sh
VPN_SERVICE_PROVIDER="custom"
VPN_TYPE="openvpn"
FIREWALL_VPN_INPUT_PORTS="6881"
OPENVPN_USER="username here (find on their site)"
OPENVPN_PASSWORD="password here (find on their site)"
```

The port has to be your TCP / µTP port configured in qBitTorrent.

## Timezone & User

Your timestamps will be weird if you don't set this up right. This is due to a combination of Docker and the VPN. `TZ` should be a `TZ identifier` from [this list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) that corresponds with the timezone you want to see timestamps from on *arr and qBitTorrent.

You should also create a user specifically for your media. Run `sudo adduser -rU media` to create a system (`-r`) user called `media` with a group (`-U`). Add yourself to the group with `sudo usermod -a -G media $USER` so you have access to the directories it uses. Go figure out where you want to store your media (mine is in `/mnt/game0/media`) and change the owner to `media` with `sudo chown -R media:media /mnt/game0/media` and then allow the group to access the data too with `sudo chmod -R g+rw /mnt/game0/media`.

Now, get the `PUID` (Process User ID) with `id -u media` and the `PGID` (Process Group ID) with `id -g media`. Place those in the config as seen below.

Here is an example of my `conf/common.env`:

```sh
TZ="America/New_York"
PUID=972
PGID=962
```
## Setting up individual components

You'll need to work through the setup parts of Prowlarr, Radarr, Sonarr, Jellyfin, Jellyseerr, and QBitTorrent.

Additionally:
- In Jellyseerr, give it the internal Jellyfin URL `http://nginx/watch` to point to Jellyfin through Nginx.
- In Prowlarr, set up flaresolvarr as a proxy (it has the address `http://gluetun:8191).
- In Prowlarr, set up QBitTorrent as a download client (it has the address `http://gluetun:5080`).
- In Prowlarr, set up Radarr and Sonarr as apps (they have the addresses `http://radarr` and `http://sonarr` respectively).
- In QBitTorrent, configure it to use whichever port has port forwarding enabled in your VPN.

## Known Issues

Jellyseerr will break with an Nginx 404 page if you use the back button in your browser. This is due to [this issue](https://github.com/sct/overseerr/issues/274) upstream with Overseerr.