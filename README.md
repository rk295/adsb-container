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
