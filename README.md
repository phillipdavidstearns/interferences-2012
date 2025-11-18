# Interferences 2012 for YOU. CAN. JUST. DO. SHIT.

**!!!Strobe Warning!!!**

2 channel video with stereo audio.

Recorded in 2012 while in residence at the Institute for Electronic Art at Alfred University. Edited and presented as a video installation in 2025 for YOU. CAN. JUST. DO. SHIT., Collective Misnomer's 9-year anniversary exhibition at The Rainbow Dome in Denver, Colorado.

The sounds are produced using a Mackie 1202 VLZ No-Input Mixing Board setup with no effects. Video was recorded using two lens-less black and white NTSC security cameras pointed at compact fluorescent lamps driven by Solid State Relays triggered by the mixing board's headphone outputs.

## Ingredients

* Media File:
	* *[Interferences 2012](https://vimeo.com/phillipstearns/interferences-2012?share=copy&fl=sv&fe=ci)*
	* 2-channel 720 x 480 SD video edited into a single 1440 x 480 file
	* export h.264 5mbps
	* Stereo audio
* 3x RPi 3 B+
	* 1x Server
	* 2x Clients
* Network router w 1G ethernet ports
	* D-Link DSR-250
* Fosi Audio BT Stereo Amplifier
* Bookshelf Stereo Speakers
* 2x NTSC Monitors

## On your main machine
 
1. Flash 3x cards with RaspberryPi Imager
	* enable SSH
	* don't setup wifi
	* username: `interferences`
	* setup network hostnames:
		* interferences-server
		* interferences-client-a
		* interferences-client-b
1. Connect all three Pis and your main machine to the same local router.
1. SSH into and install dependencies on each pi:
	* Update and upgrade: `sudo apt update && sudo apt upgrade -y`
	* vlc: `sudo apt-get install git vlc`
1. Clone this repo `git@github.com:phillipdavidstearns/interferences-2012.git`
1. `cd interferences-2012`
1. Create a .env file: `nano .env`

```
HOST=<server_network_hostname>
PORT=8554
FILE_PATH=<path>
DISPLAY=:0
XDG_RUNTIME_DIR=/run/user/1000

```

Where `<server_network_hostname>` is the name of your media server pi's network hostname, e.g. `interferences-server.local` and `<path>` is the locaion of the media file on the server machine, e.g. `/home/interferences/interferences/media_file.mp4`


## Installation: Server

1. From repo directory on your main machine, run `rsync -vaPu . interferences-server:~/interferences` to copy files to the server.
1. Create links to the appropriate service file for the server: `sudo ln -s /home/interferences/interferences/interferences-server.service /lib/systemd/system/`
1. Reload the systemd daemon `sudo systemctl daemon-reload`
1. Start the service `sudo systemctl start interferences-server`
1. Enable the service `sudo systemctl enable interferences-server`

## Installation: Client

1. From repo directory on your main machine, run `rsync -vaPu . interferences-client-<client_id>:~/interferences` to copy files to the clients where `<client_id>` matches your host naming convention.
1. Create links to the appropriate service file for the client: `sudo ln -s /home/interferences/interferences/interferences-client@.service /lib/systemd/system/`
1. Reload the systemd daemon `sudo systemctl daemon-reload`
1. Start the service `sudo systemctl start interferences-client@<align_value>` where `<align_value>` is 1 for left and 2 for right. This effectively selects between showing the left side of the composition.
1. Enable the service `sudo systemctl enable interferences-client@<align_value>`

## Connections:

For the installation the Network connections are all wired ethernet connections.

* D-Link DSR-250 has the following Ethernet connections:
	* `interferences-server`
	* `interferences-client-a`
	* `interferences-client-b`
* Monitor A is connected to `interferences-client-a` with a 1/8" TRRS to RCA AV cable using the *RED* RCA plug.
* Monitor B is connected to `interferences-client-b` with a 1/8" TRRS to RCA AV cable using the *RED* RCA plug.
* The audio amplifier inputs are connected to the client Pis via a 1/8" TRS to RCA cable via barrel turn around adapters.
	* The YELLOW `interferences-client-a` output RCA connects to the BLACK audio input RCA
	* The WHITE `interferences-client-b` output RCA connects to the RED audio input RCA

## Notes:

### vlc commands

These commands start and receive RTSP media streams on the respective machines:

* Server side: `cvlc --loop ./Interferences-YCJDS.mp4 --sout="#gather:rtp{sdp=rtsp://:8554/stream}" --network-caching=1500 --sout-all --sout-keep` [source](https://stackoverflow.com/questions/25648337/using-vlc-to-host-a-stream-of-an-infinite-video-loop)
* Client side: `cvlc --no-osd --align=2 --no-autoscale --deinterlace=1 --grayscale --play-and-exit --one-instance rtsp://interferences-server.local:8554/stream` [manual](https://wiki.videolan.org/VLC_command-line_help/)

