# Full Setup for current (December 2020) Raspbian

From a fresh minimal install of Raspbian, these are the steps I took:

```bash
apt update                                          # Need to update the package lists
apt upgrade -y                                      # Always good to upgrade
apt install -y python3 python3-pip tmux vim         # docker-compose needs Python 3 and tools
pip3 install docker-compose                         # Install docker-compose
curl -fsSL https://get.docker.com -o get-docker.sh  # Get docker installer
sh get-docker.sh                                    # Install docker
usermod -aG docker pi                               # Put the pi user in the docker group

# Generate kernel module blacklist
cat <<EOF > /etc/modprobe.d/blacklist-rtl2832.conf
# Blacklist RTL2832 so docker container readsb can use the device

blacklist rtl2832
blacklist dvb_usb_rtl28xxu
blacklist rtl2832_sdr
EOF
```
