# ReadSB Container compose file

A (very) brief list of what I did on a fresh minimal copy of Raspbian is [here](Raspbian.md).

This is the `readsb` docker compose file I use on my Pi 3. I have a [FlightAware Pro Stick Plus](https://thepihut.com/products/flightaware-pro-stick-plus-usb-sdr-ads-b-receiver). Connected to a £5 aerial in the loft. This gives me about 100 NM range.

I've tried to litter the `docker-compose.yml` and the `env-template` files with comments, most of the docs are in there.

## Prerequisites

### Docker

Docs here: https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script

#### tl;dr

```
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

### docker-compose

The latest Raspbian (as of 2020-12-08) ships with an old version of `docker-compose`. Once docker is installed run:

```
$ apt install -y python3 python3-pip
$ pip3 install docker-compose
```

Then it should be in `/usr/local/bin/`

### Blacklist Modules

Before this container will work properly, you must blacklist the kernel modules for the RTL-SDR USB device from the host's kernel.

To do this, create a file /etc/modprobe.d/blacklist-rtl2832.conf containing the following:

```
# Blacklist RTL2832 so docker container readsb can use the device

blacklist rtl2832
blacklist dvb_usb_rtl28xxu
blacklist rtl2832_sdr
```

## Setup

Rename the `env-template` to `.env` and adjust the variablers. The most important are:

* `BUS`
* `DEVICE`
* `LAT`
* `LONG`
* `ALT`

`BUS` and `DEVICE` can be read from the output of `lsusb`, for example:

```
$ lsusb
Bus 001 Device 004: ID 0bda:2832 Realtek Semiconductor Corp. RTL2832U DVB-T
Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp. SMSC9512/9514 Fast Ethernet Adapter
Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp. SMC9514 Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Look for a Realtek Semiconductor Corp entry, in this example its Bus 1 and Device 4.

## Other containers

Optionally get yourself some keys for the other services. They are all documented in the `env-template` file.

If you dont' want any other services its safe to comment out all of the rest of the `docker-compose.yaml` after the `readsb` container.

## 360 Radar

Mike, who built all the other containers used here used to provide a container for 360 Radar, however the owners of the site have requested he removes it from Docker Hub. Because of this you need to provide your own. He has however left the Git repo up for you to clone [here](https://github.com/mikenye/docker-360radar).

To build this do something like the following:

```
% git clone https://github.com/mikenye/docker-360radar.git
% cd docker-360radar
% docker build -t 360radar:latest .
```

The two `THREE_SIXTY_NAME` and `THREE_SIXTY_TAG` variables in the `env-template` should already be  set to `360radar` and `latest`, but it is worth a check.

Next jump to the bottom of the `docker-compose.yml` file and remove the comment characters from the 360 Radar container.

```
% docker-compose up -d 360radar
```

Will bring it up, run `docker-compose logs -f 360radar` to tail the logs and check it is working. Sample output should look like this:

```
360radar    | [s6-init] making user provided files available at /var/run/s6/etc...exited 0.
360radar    | [s6-init] ensuring user provided files have correct perms...exited 0.
360radar    | [fix-attrs.d] applying ownership & permissions fixes...
360radar    | [fix-attrs.d] done.
360radar    | [cont-init.d] executing container initialization scripts...
360radar    | [cont-init.d] 01-sanitycheck: executing... 
360radar    | [cont-init.d] 01-sanitycheck: exited 0.
360radar    | [cont-init.d] done.
360radar    | [services.d] starting services
360radar    | [services.d] done.
360radar    | [mlat-client] Wed Dec 23 18:46:05 2020 mlat-client 0.2.11 starting up
360radar    | [mlat-client] Wed Dec 23 18:46:05 2020 Connected to multilateration server at test.mlat-server.co.uk:41111, handshaking
360radar    | [mlat-client] Wed Dec 23 18:46:05 2020 Server says: 
360radar    | [mlat-client] 
360radar    | [mlat-client]         In-development v2 server. Expect odd behaviour.
360radar    | [mlat-client] 
360radar    | [mlat-client]         The multilateration server source code is available under
360radar    | [mlat-client]         the terms of the Affero GPL (v3 or later). You may obtain
360radar    | [mlat-client]         a copy of this server's source code at the following
360radar    | [mlat-client]         location: https://github.com/mutability/mlat-server
360radar    | [mlat-client]         
360radar    | [mlat-client] Wed Dec 23 18:46:06 2020 Handshake complete.
360radar    | [mlat-client] Wed Dec 23 18:46:06 2020   Compression:       zlib2
360radar    | [mlat-client] Wed Dec 23 18:46:06 2020   UDP transport:     test.mlat-server.co.uk:41111
360radar    | [mlat-client] Wed Dec 23 18:46:06 2020   Split sync:        disabled
360radar    | [mlat-client] Wed Dec 23 18:46:06 2020 Input connected to readsb:30005
360radar    | [mlat-client] Wed Dec 23 18:46:06 2020 Input format changed to BEAST, 12MHz clock
360radar    | [mlat-client] Wed Dec 23 19:01:05 2020 Receiver status: connected
360radar    | [mlat-client] Wed Dec 23 19:01:05 2020 Server status:   ready
```

Once that is done, use the contact form [here](https://radar.lowflyingwales.co.uk/contact-us/) to drop them a line and tell them your site name.


