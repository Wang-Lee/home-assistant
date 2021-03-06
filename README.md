# Home Assistant [![Build Status](https://travis-ci.org/balloob/home-assistant.svg?branch=master)](https://travis-ci.org/balloob/home-assistant) [![Coverage Status](https://img.shields.io/coveralls/balloob/home-assistant.svg)](https://coveralls.io/r/balloob/home-assistant?branch=master)

This is the source for Home Assistant. For installation instructions, tutorials and the docs, please see [https://home-assistant.io](https://home-assistant.io). For a functioning demo frontend of Home Assistant, [click here](https://home-assistant.io/demo/).

Home Assistant is a home automation platform running on Python 3. The goal of Home Assistant is to be able to track and control all devices at home and offer a platform for automating control.

It offers the following functionality through built-in components:

 * Track if devices are home by monitoring connected devices to a wireless router (supporting [OpenWrt](https://openwrt.org/), [Tomato](http://www.polarcloud.com/tomato), [Netgear](http://netgear.com))
 * Track and control [Philips Hue](http://meethue.com) lights
 * Track and control [WeMo switches](http://www.belkin.com/us/Products/home-automation/c/wemo-home-automation/)
 * Track and control [Google Chromecasts](http://www.google.com/intl/en/chrome/devices/chromecast)
 * Track running services by monitoring `ps` output
 * Track and control [Tellstick devices and sensors](http://www.telldus.se/products/tellstick)
 * Turn on the lights when people get home after sun set
 * Turn on lights slowly during sun set to compensate for light loss
 * Turn off all lights and devices when everybody leaves the house
 * Offers web interface to monitor and control Home Assistant
 * Offers a [REST API](https://home-assistant.io/developers/api.html) for easy integration with other projects ([see related projects for Android and Ruby support](#related_projects))
 * [Ability to have multiple instances of Home Assistant work together](https://home-assistant.io/developers/architecture.html)

Home Assistant also includes functionality for controlling HTPCs:

 * Simulate key presses for Play/Pause, Next track, Prev track, Volume up, Volume Down
 * Download files
 * Open URLs in the default browser

[![screenshot-states](https://raw.github.com/balloob/home-assistant/master/docs/screenshots.png)](https://home-assistant.io/demo/)

The system is built modular so support for other devices or actions can be implemented easily. See also the [section on architecture](https://home-assistant.io/developers/architecture.html) and the [section on creating your own components](https://home-assistant.io/developers/creating_components.html).

If you run into issues while using Home Assistant or during development of a component, reach out to the [Home Assistant developer community](https://groups.google.com/forum/#!forum/home-assistant-dev).

## Installation instructions / Quick-start guide

Running Home Assistant requires that python3 and the package requests are installed. Run the following code to install and start Home Assistant:

```python
git clone --recursive https://github.com/balloob/home-assistant.git
cd home-assistant
pip3 install -r requirements.txt
python3 -m homeassistant --open-ui
```

The last command will start the Home Assistant server and launch its webinterface. By default Home Assistant looks for the configuration file `config/home-assistant.conf`. A standard configuration file will be written if none exists.

If you are still exploring if you want to use Home Assistant in the first place, you can enable the demo mode by adding the `--demo-mode` argument to the last command.

If you're using Docker, you can use

```bash
docker run -d --name="home-assistant" -v /path/to/homeassistant/config:/config -v /etc/localtime:/etc/localtime:ro --net=host balloob/home-assistant
```

After you have launched the Docker image, navigate to its web interface on [http://127.0.0.1:8123](http://127.0.0.1:8123).

After you got the demo mode running it is time to enable some real components and get started. An example configuration file has been provided in [/config/home-assistant.conf.example](https://github.com/balloob/home-assistant/blob/master/config/home-assistant.conf.example).

*Note:* you can append `?api_password=YOUR_PASSWORD` to the url of the web interface to log in automatically.

*Note:* for the light and switch component, you can specify multiple platforms by using sequential sections: [switch], [switch 2], [switch 3] etc

### Philips Hue
To get Philips Hue working you will have to connect Home Assistant to the Hue bridge.

Run the following command from your config dir and follow the instructions:

```bash
python3 -m phue --host HUE_BRIDGE_IP_ADDRESS --config-file-path phue.conf
```

After that add the following lines to your `home-assistant.conf`:

```
[light]
platform=hue
```

### Wireless router

Your wireless router is used to track which devices are connected. Three different types of wireless routers are currently supported: tomato, netgear and luci (OpenWRT). To get started add the following lines to your `home-assistant.conf` (example for Netgear):

```
[device_tracker]
platform=netgear
host=192.168.1.1
username=admin
password=MY_PASSWORD
```

*Note on tomato:* Tomato requires an extra config variable called `http_id`. The value can be obtained by logging in to the Tomato admin interface and search for `http_id` in the page source code.

*Note on luci:* before the Luci scanner can be used you have to install the luci RPC package on OpenWRT: `opkg install luci-mod-rpc`.

Once tracking, the `device_tracker` component will maintain a file in your config dir called `known_devices.csv`. Edit this file to adjust which devices have to be tracked.

As an alternative to the router-based device tracking, it is possible to directly scan the network for devices by using nmap. The IP addresses to scan can be specified in any format that nmap understands, including the network-prefix notation (`192.168.1.1/24`) and the range notation (`192.168.1.1-255`).

```
[device_tracker]
platform=nmap_tracker
hosts=192.168.1.1/24
```
