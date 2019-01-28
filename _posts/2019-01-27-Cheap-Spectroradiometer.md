---
layout: post
title: Cheap Spectroradiometer
date:   2019-01-27 18:00:00 -0700
categories: 
---

* Install [I2C Star](https://github.com/daniel-thompson/i2c-star)

...

* Install i2ctools

    This contains a bunch of small helper tools that make it possible to
    access I2C devices that are connected to I2C master on your
    Linux system.
    

```
sudo apt-get install i2c-tools
```

* Build quick prototype
* Test I2C Star operational

```
sudo i2cdetect -l
i2c-3   unknown     DPDDC-B                             N/A
i2c-1   unknown     i915 gmbus dpb                      N/A
i2c-6   unknown     i2c-tiny-usb at bus 001 device 060  N/A
i2c-4   unknown     DPDDC-C                             N/A
i2c-2   unknown     i915 gmbus dpd                      N/A
i2c-0   unknown     i915 gmbus dpc                      N/A
i2c-5   unknown     DPDDC-D                             N/A
```

* Scan devices on the I2C bus

```
tom@thinkcenter:~/projects/as7265x$ sudo i2cdetect -y 6
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- -- 49 -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: -- -- -- -- -- -- -- --                         
```

Success!
I2C device address 0x49 is the I2C address of the AS7265X!

* Check out [the datasheet of the AS7265X](https://ams.com/documents/20143/36005/AS7265x_DS000612_1-00.pdf/08051c8a-a7f6-6231-7993-2d3fe0bf38b8#page=23).

    I2C related stuff starts at page 23.

    The register map starts at page 28.

* Read the HW Version register

`run.sh` script:
```
I2C_PORT=6
I2C_ADDR=0x49
STATUS_REG=0x00
WR_REG=0x01
RD_REG=0x02

echo "Status:"
i2cget -y $I2C_PORT $I2C_ADDR $STATUS_REG

# Select address 0 for read
i2cset -y $I2C_PORT $I2C_ADDR $WR_REG 0x00
echo "Addr 0:"
i2cget -y $I2C_PORT $I2C_ADDR $RD_REG


i2cset -y $I2C_PORT $I2C_ADDR $WR_REG 0x01
echo "Addr 1:"
i2cget -y $I2C_PORT $I2C_ADDR $RD_REG
```

According to figure 27: "HW Version Registers", we should read a value of 0x40 for address 0x00
and 0x41 for address 0x01.

This prints:
```
tom@thinkcenter:~/projects/as7265x$ sudo ./run.sh 
Status:
0x80
Addr 0:
0x40
Addr 1:
0x41
```

SUCCESS!

* Install I2C development library

```sudo apt-get install libi2c-dev```


