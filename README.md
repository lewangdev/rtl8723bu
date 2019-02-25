How To
=====

```
# Update repo and install kernel-headers
sudo apt update
sudo apt-get dist-upgrade
sudo apt install raspberrypi-kernel-headers bc

# Build
make 
sudo make install

# Enable module
sudo modprobe -v 8723bu
```
