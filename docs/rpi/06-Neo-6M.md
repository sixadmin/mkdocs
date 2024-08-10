https://www.instructables.com/Raspberry-Pi-the-Neo-6M-GPS/
https://github.com/FranzTscharf/Python-NEO-6M-GPS-Raspberry-Pi

sudo apt-get install python3-pip

   11  python3 -m venv venv
   12  source venv/bin/activate
   14  pip install pynmea2


   sudo apt-get install gpsd gpsd-clients python3-gps minicom

  sudo vim /boot/firmware/config.txt


```
dtparam=spi=on
dtoverlay=pi3-disable-bt
core_freq=250
enable_uart=1
force_turbo=1
init_uart_baud=9600
```

  sudo vim /boot/firmware/cmdline.txt

On efface tout et on met à la place

dwc_otg.lpm_enable=0 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles


sudo reboot

sudo killall gpsd
sudo nano /etc/default/gpsd

Edit the file /etc/default/gpsd and add your serial port in DEVICES, like

DEVICES="/dev/ttyAMA0"

sudo systemctl enable gpsd.socket
sudo systemctl start gpsd.socket 
sudo cgps -s

```
┌───────────────────────────────────────────┐┌──────────────────Seen 16/Used  0┐
│ Time:          n/a (0)                    ││GNSS   PRN  Elev   Azim   SNR Use│
│ Latitude:        n/a                      ││GP 11   11   n/a    0.0   0.0  N │
│ Longitude:       n/a                      ││GP 12   12   n/a    0.0   0.0  N │
│ Alt (HAE, MSL):        n/a,       n/a     ││GP 13   13   n/a    0.0   0.0  N │
│ Speed:           n/a                      ││GP 14   14   n/a    0.0   0.0  N │
│ Track (true, var):                n/a deg ││GP 15   15   n/a    0.0   0.0  N │
│ Climb:           n/a                      ││GP 16   16   n/a    0.0   0.0  N │
│ Status:         NO FIX (1504 secs)        ││GP 17   17   n/a    0.0   0.0  N │
│ Long Err  (XDOP, EPX):  n/a ,  n/a        ││GP 18   18   n/a    0.0   0.0  N │
│ Lat Err   (YDOP, EPY):  n/a ,  n/a        ││GP 19   19   n/a    0.0   0.0  N │
│ Alt Err   (VDOP, EPV): 99.99,  n/a        ││GP 20   20   n/a    0.0   0.0  N │
│ 2D Err    (HDOP, CEP): 99.99,  n/a        ││GP 21   21   n/a    0.0   0.0  N │
│ 3D Err    (PDOP, SEP): 99.99,  n/a        ││GP 22   22   n/a    0.0   0.0  N │
│ Time Err  (TDOP):      99.99              ││GP 23   23   n/a    0.0   0.0  N │
│ Geo Err   (GDOP):      99.99              ││GP 24   24   n/a    0.0   0.0  N │
│ ECEF X, VX:              n/a    n/a       ││GP 25   25   n/a    0.0   0.0  N │
│ ECEF Y, VY:              n/a    n/a       ││SB120   33   n/a    0.0   0.0  N │
│ ECEF Z, VZ:              n/a    n/a       ││                                 │
│ Speed Err (EPS):        n/a               ││                                 │
│ Track Err (EPD):        n/a               ││                                 │
│ Time offset:            n/a               ││                                 │
│ Grid Square:            n/a               ││                                 │
└───────────────────────────────────────────┘└─────────────────────────────────┘


```

https://www.geekering.com/categories/embedded-sytems/raspberry-pi/rubenmarques/how-to-use-neo-6m-gps-with-rpi-begginer-friendly/