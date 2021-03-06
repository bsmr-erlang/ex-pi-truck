Truck
=====

An Elixir Program that runs on my [Raspberry Pi A+](https://www.raspberrypi.org/products/model-a-plus/) and controls an RX transmitter to send commmands to a cheap [RC Truck](http://www.walmart.com/ip/New-Bright-1-24-Radio-Control-Full-Function-Jeep-Wrangler-Green/38069358). I got a lot of help from reading [a blog post from Andrew Chalkley](http://www.forefront.io/a/hacking-9-buck-remote-controlled-car-with-arduino) who used an Arduino with a similar RC car.

## Hardware Setup
In order to minimize reverse-engineering I decided to hack into the transmitter side of this little RC truck.

For power I have hooked up Ground ([Pin 6](http://pi.gadgetoid.com/pinout/ground)) to negative battery terminal and 3.3V ([Pin 1](http://pi.gadgetoid.com/pinout/pin1_3v3_power)) to the postitive battery terminal on the transmitter.

I've also soldered some leads to the solder points on the transmitter named:

* TP2 Turn Wheels Right
* TP9 Turn Wheels Left
* TP6 Drive Forward
* TP7 Drive Backwards

Then I connect these leads to the Pi like this:

* TP2 => [Pin 11](http://pi.gadgetoid.com/pinout/pin11_gpio17)
* TP9 => [Pin 15](http://pi.gadgetoid.com/pinout/pin15_gpio22)
* TP6 => [Pin 16](http://pi.gadgetoid.com/pinout/pin16_gpio23)
* TP7 => [Pin 18](http://pi.gadgetoid.com/pinout/pin18_gpio24)

## Software Setup
Similar to my [Raspberry Pi Clock](https://github.com/mmmries/pi-alarm-clock) I am using Elixir and a Raspberry Pi.

### Setting Up The Pi
From vanilla Raspbian (2015-05-15) use a wired ethernet (or USB ethernet) connection and boot the pi.

SSH into the pi with the default credentials:

`username: pi`
`password: raspberry`

Now run `sudo raspi-config` and make the following changes.

* Expand the file system to use the whole MicroSD Card
* Change the default user password (optional)
* Change the default locale to en-US UTF-8 UTF-8
* Change the timezone to America/Denver
* Change the keyboard layout to a US layout
* Change the hostname to pi1

### Configure WiFi
This setup is based on [these WiFi Settings](http://www.andreagrandi.it/2014/09/02/how-to-configure-edimax-ew-7811un-wifi-dongle-on-raspbian/).

* `nano /etc/network/interfaces`

```
auto lo
iface lo inet loopback

auto eth0
allow-hotplug eth0
iface eth0 inet manual

allow-hotplug wlan0
auto wlan0

iface wlan0 inet dhcp
wpa-ssid YOURESSID
wpa-psk YOURWPAPASSWORD
```

### Install Erlang-mini
```
echo "deb http://packages.erlang-solutions.com/debian wheezy contrib" >> /etc/apt/sources.list
wget http://packages.erlang-solutions.com/debian/erlang_solutions.asc
sudo apt-key add erlang_solutions.asc && rm erlang_solutions.asc
sudo apt-get update
apt-get install -y --force-yes erlang-mini upstart htop
# You will have to confirm the switch to upstart by typing 'Yes, do as I say!'
```

### Install Precompiled Elixir
```
mkdir /opt/elixir-1.0.4
curl  -L https://github.com/elixir-lang/elixir/releases/download/v1.0.4/Precompiled.zip -o /opt/elixir-1.0.4/precompiled.zip
cd /opt/elixir-1.0.4
unzip precompiled.zip
echo 'export PATH=/opt/elixir-1.0.4/bin:$PATH' >> /etc/bash.bashrc
```
