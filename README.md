# ReadSB Container compose file

This is the `readsb` docker compose file I use on my Pi 3. I have a [FlightAware Pro Stick Plus](https://thepihut.com/products/flightaware-pro-stick-plus-usb-sdr-ads-b-receiver). Connected to a £5 aerial in the loft. This gives me about 100 NM range.

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
$ pip3 install docker-compose
```

Then it should be in `/usr/local/bin/`

## Setup

Rename the `env-template` to `.env` and adjust the variablers. The most important are:

* `BUS`
* `DEVICE`
* `LAT`
* `LONG`
* `ALT`

Optionally get yourself a Flight Radar 24 Key and a ADSB Exchange UUID - docs in the `env-template` file.

If you don't want to feed Flight Radar 24, comment out the container in `docker-compose.yaml`, same with ADSB Exchange.
