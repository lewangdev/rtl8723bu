How To
=====

```
# Update repo and install kernel-headers
sudo apt update
sudo apt install raspberrypi-kernel-headers

# Build
make 
sudo make install

# Enable module
sudo modprobe -v 8723bu
```
