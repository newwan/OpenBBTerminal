# Docker Advanced Settings

## Display

In order to display plots in the docker container, we need to configure the XServer on the host machine.
Without this configuration the interactive charts will not be displayed.

### X-Server on macOS

Users familiar with Docker and X-Server can set the `DISPLAY` variable in the file [setenv](/docker/setenv) described above. If you use this approach remember to add `:0` at the end of your inet address. E.g. `DISPLAY=192.168.1.155:0`.

For help setting up the X-Server continue reading:

#### Setting up X Quartz/X11

On macOS the X11 client of choice is [XQuartz](https://www.xquartz.org/). On Windows it's [Xming](http://www.straightrunning.com/XmingNotes/). XQuartz will be used as an example further on.

0. Install X Quartz from <https://www.xquartz.org/>
1. With X Quartz open: go to Preferences -> Security and make sure both options are enabled.
   ![Screen Shot 2021-09-08 at 12 21 48 PM](https://user-images.githubusercontent.com/18151143/132548605-235d774b-9aa6-4a45-afcf-58fb775d376a.png)

#### Adding the display for Docker

From the command prompt or terminal, run the following to add your local configuration to the list of allowed access control:

```bash
IP=$(ifconfig | grep inet | grep -v -e "127.0.0.1" | awk '$1=="inet" {print $2}')
xhost + $IP
```

Now we can run the docker container, adding the display to the environment:

```bach
docker run -it --rm --env-file=path/to/setenv -e DISPLAY=$IP:0 ghcr.io/openbb-finance/openbbterminal-poetry:latest
```

This container will be able to display all the same plots as the terminal interface.

### X-Server on Linux Desktop

X-Server is default in Linux distribution. There is no need to install any clients.

#### Local docker container

We can use IPC socket to connect Desktop.

Add this setting to your `.env` file.

```bash
OPENBB_BACKEND=Qt5Agg
```

And run the following commands.

```bash
xhost +local:
docker run -it --rm --name openbb --env-file=./.env -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix ghcr.io/openbb-finance/openbbterminal-poetry:latest
xhost -local:
```

If you're using remote docker host, you can connect with "ssh -X <FQDN/IP>".

Then run the previous docker command.
