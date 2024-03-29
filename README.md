# homebridge-g810-led

This homebridge plugin allows controlling the lighting of Logitech keyboards under linux using [g810-led](https://github.com/MatMoul/g810-led).

## Installing and homebridge plugin

The homebridge plugin will create a websocket server which provides the values that the homebridge receivies using homekit.

Install the plugin using npm:

```shell
$ sudo npm install -g homebridge-logitech-keyboard
```

After that restart your homebridge and create a new accessory. Here's a example config:

```json
{
    "accessory": "G810-led",
    "name": "My Keyboard",
    "port": 50000,
    "rgb": false
}
```

You can change `name` to distinglish your keyboard.

The `port` parameter is setting the port of the websocket server that will run on the homebridge. You can change it if you want to connect more than one keyboard.

You should set `rgb` to `true` if your keyboard supports RGB. If it is set to `false` you only have controll over the brightness and it will always show white, this should be used if your keyboard has only white leds.

## Installing the client on your linux machine

This client will connect to the websocket server of this plugin, get the color and use `g810-led` to update the lighting of your keyboard. You need to have [g810-led](https://github.com/MatMoul/g810-led/blob/master/INSTALL.md) installed.

A precompiled static binary for x86_64 is available in each GitHub release. Download it and add it to your `PATH`. If you need it for another architecutre you'll need to compile it yourself using `go build` in the `go-client` directory.

Temporarily start the client using this command:

```shell
$ homebridge-g810-led-client --command g810-led --server ws://<homebridge-ip>:<port>
```

You may want to change the `--command` parameter to use the correct command for your keyboard. Refer to [this](https://github.com/MatMoul/g810-led#help-) page to see all available commands.

The `--server` parameter sets the server. Change the ip and port accordingly.

There is also a `--disableWhenIdle` flag that will disable the lighting of your keyboard when your computer is idling for extended periods of time or locked. This feature is disabled by default and you have to enable it by passing the `--disableWhenIdle` flag to the client.

After you executed the command you can test whether everything works.

As a permanent install I recommend creating a systemd service unit to start the client if you start your computer.

To use a systemd service create a file at `/etc/systemd/system/homebridge-g810.service` with the following contents:
```
[Unit]
Description=Homebridge g810-led client
After=network-online.target

[Service]
Type=simple
Restart=on-failure
RestartSec=10s
ExecStart=/bin/sh -c 'homebridge-g810-led-client --command g810-led --server ws://<homebridge-ip>:<port>'
User=<yourUserName>

[Install]
WantedBy=multi-user.target
```

Then reload systemd and enable the service:

```shell
$ sudo systemctl daemon-reload
$ sudo systemctl enable homebridge-g810.service
$ sudo systemctl start homebridge-g810.service
```
