# installation of gnuradio

* Starting GNU Radio from console ：`gnuradio-companion`

* Starting GNU Radio from GUI:

If you get the ModuleNotFoundError error while starting GNU Radio  from GUI, but can start GNU Radio using the terminal, it is because the  environment variables are set for the terminal, but not for your  graphical environment. Try one of the listed solutions below.

1. Store the export commands  in `~/.profile`. Log out and back in for the changes to take effect.
2. Store the export commands in a new shell script (with any name of your choice) in `/etc/profile.d/`. Restart your computer thereafter.

---

1. Ubuntu 20.04.2 , Linux 5.11.0-37-generic
2. gnuradio 3.8
3. iio libiio
4. gr-iio

## 2 gnuradio

There are three methords for installation.

### 2.1  From Binaries

`apt install gnuradio`


### 2.2  PPA Installation ⬅️

For Ubuntu, the latest builds (both released and pulled from master branch) are maintained as PPAs on [launchpad](https://launchpad.net/~gnuradio).  Be sure to uninstall any previously installed versions of gnuradio first.

To access the latest from the master branch, add the  gnuradio/gnuradio-master ppa (removing other gnuradio ppas if already  configured)

```
$ sudo add-apt-repository ppa:gnuradio/gnuradio-releases-3.8
$ sudo apt-get update
$ sudo apt install gnuradio
```


### 2.3 Using PyBOMBS 

PyBOMBS is good at building GNU Radio, UHD, and various Out of Tree (OOT) modules from source and then installing into a specified user directory rather than in the system files.
 PyBOMBS detects the user's Operating System and loads all of the prerequisites in the first stage of the build. 

## 3 libiio

### 3.1  Preinstallation

   Make sure the following modules are installed in your kernel:

```
cdc-acm,cdc_ether
rndis_host
rndis_wlan
usbnet 
```

These will be installed/compiled by default on Ubuntu 20.4.

In order to access some USB functions without root privileges, it's recommended to install the PlutoSDR udev rules. Simply download [53-adi-plutosdr-usb.rules](https://raw.githubusercontent.com/analogdevicesinc/plutosdr-fw/master/scripts/53-adi-plutosdr-usb.rules)  and copy into into the /etc/udev/rules.d/ folder. You can also use this [this plutosdr-m2k-udev.deb package ](https://wiki.analog.com/_media/university/tools/pluto/drivers/plutosdr-m2k-udev_1.0_amd64.deb)  to install the PlutoSDR udev rules. To install the debian package, use one of the following: `sudo apt-get install ./plutosdr-m2k-udev.deb` or `sudo dpkg -i plutosdr-m2k-udev.deb`.
Afterwards reload rules or restart udev, using either `udevadm control --reload-rules` or `sudo service udev restart`.

example `dmesg|grep PLUTO`
When plugging Pluto in , you will see kernel messages of  *PLUTO*

### 3.2  IIO devices

`$ sudo apt-get install libiio-utils `

Try to make sure you can talk to the device, and find the IIO devices:

```
iio_info -n 192.168.2.1 | grep device
```

## 4 gr-iio

### 4.1 Dependencies

gr-iio requires a few main dependencies:

-  [libiio](https://wiki.analog.com/resources/tools-software/linux-software/libiio)
-  [libad9361](https://github.com/analogdevicesinc/libad9361-iio)
-  GNU Radio and its development packages
-  swig (Optional for python support)

Since GNU Radio can come from many  different sources and with many different packages includes we will try  to cover all the necessary dependencies but this can differ depending on OS packaging. If you have built and installed gnuradio from source yourself you should be good to go. Otherwise, consult the [GNU Radio Wiki](https://wiki.gnuradio.org/index.php/InstallingGR) for further documentation on the development installation.

Most dependencies do have pre-built binaries available on github ([libiio](https://github.com/analogdevicesinc/libiio/releases), [libad9361](https://github.com/analogdevicesinc/libad9361-iio/releases)) or can be build from source as below.

#### Download and build libiio

Libiio requires the following packages:

-  libxml2
-  libxml2-dev
-  bison
-  flex
-  cmake
-  git
-  libaio-dev

Install with apt:

```
(sudo) apt install libxml2 libxml2-dev bison flex cmake git libaio-dev libboost-all-dev
```

If you want the documentation for the C API you will require doxygen:

```
(sudo) apt install doxygen
```

If you want the USB backend add libusb support:

```
(sudo) apt install libusb-1.0-0-dev
```

If you want zeroconf add avahi support:

```
(sudo) apt install libavahi-common-dev libavahi-client-dev
```

Build and install libiio from source:

```
git clone https://github.com/analogdevicesinc/libiio.git
cd libiio
cmake .
make 
sudo make install
cd ..
```

#### Download and build libad9361-iio

Build and install libiio from source:

```
git clone https://github.com/analogdevicesinc/libad9361-iio.git
cd libad9361-iio
cmake .
make 
sudo make install
cd ..
```

### 4.2 GNU Radio and gr-iio

If you did not install libiio from source you will need the following packages as well:

-  bison
-  flex
-  cmake
-  git
-  libgmp-dev

Install with apt:

```
(sudo) apt install bison flex cmake git libgmp-dev
```

For GNU Radio 3.8 to **enable** python support requires swig:

```
(sudo) apt install swig
```

Since GNU Radio 3.8 is not fully  mainstream across package managers 3.8+ support requires a non-master  branch, specifically upgrade-3.8. To get GNU Radio 3.8+ consult the [GNU Radio wiki](https://wiki.gnuradio.org/index.php/InstallingGR#Ubuntu_PPA_Installation).

gr-iio in 3.8+ requires liborc-dev

```
(sudo) apt install liborc-dev
```

Build and install gr-iio from source:

```
git clone -b upgrade-3.8 https://github.com/analogdevicesinc/gr-iio.git
cd gr-iio
cmake .
make 
sudo make install
cd ..
sudo ldconfig
```

### 4.3 Post installation

For 3.8, make sure the gr-iio swig interface is on your PYTHONPATH.  Otherwise, you will get import errors in python. The common command  would be (depending on OS and install location):

```
export PYTHONPATH=$PYTHONPATH:/usr/local/lib/python3/dist-packages
```

The added path is the location of the newly installed **iio** folder.

