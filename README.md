# spotify-adblock
Spotify adblocker for Linux (macOS untested) that works by wrapping `getaddrinfo` and `cef_urlrequest_create`. It blocks requests to domains that are not on the allowlist, as well as URLs that are on the denylist.

### Notes
* This **does not** work with the snap Spotify package.
* This **might not** work with the Flatpak Spotify package, depending on your system's shared libraries' versions.
* On Debian-based distributions (e.g. Ubuntu), the Debian Spotify package can be installed by following the instructions at the bottom of [this page](https://www.spotify.com/us/download/linux/). *(recommended)*
* If you are on Arch make sure to use AUR version of spotify as it is easier to set up than the flatpak

## Build
Prerequisites:
* Git
* Make
* Rust
* [Cargo](https://doc.rust-lang.org/cargo/) - Since this is a rust package you need cargo in order to make and build the adblock


```bash
$ git clone https://github.com/abba23/spotify-adblock.git
$ cd spotify-adblock
$ make
```

## Install
```bash
$ sudo make install
```

If the make fails then you can try this command. Remeber you must be in the same directory as the git hub project.
```
cargo build --release
```

#### Flatpak
```bash
$ mkdir -p ~/.spotify-adblock && cp target/release/libspotifyadblock.so ~/.spotify-adblock/spotify-adblock.so
$ mkdir -p ~/.var/app/com.spotify.Client/config/spotify-adblock && cp config.toml ~/.var/app/com.spotify.Client/config/spotify-adblock
$ flatpak override --user --filesystem="~/.spotify-adblock/spotify-adblock.so" --filesystem="~/.config/spotify-adblock/config.toml" com.spotify.Client
```

## Usage
### Command-line

This line must be used inside the same directory where you built the package - this is a pain

```bash
$ LD_PRELOAD=/usr/local/lib/spotify-adblock.so spotify
```

or for the fish shell

```fish
env LD_PRELOAD=/usr/local/lib/spotify-adblock.so spotify
```

### Quaility of life - In case something goes wrong
This is my contribution to this particular project:
I have created spotify-start.sh which is the script you should be referencing when trying to run the adblocked Spotify from anywhere in the terminal.
Check below for the updated .desktop file under the Debian/Arch section 
The spotify-start.sh also allows you to use a keybind if you are using a tiling window manager like i3






#### Flatpak
```bash
$ flatpak run --command=sh com.spotify.Client -c 'eval "$(sed s#LD_PRELOAD=#LD_PRELOAD=$HOME/.spotify-adblock/spotify-adblock.so:#g /app/bin/spotify)"'
```

### Desktop file
You can integrate it with your desktop environment by creating a `.desktop` file (e.g. `spotify-adblock.desktop`) in `~/.local/share/applications`. This lets you easily run it from an application launcher without opening a terminal.

Examples:

<details> 
  <summary>Debian/Arch AUR Package</summary>
  <p>

```
[Desktop Entry]
Type=Application
Name=Spotify (adblock)
GenericName=Music Player
Icon=spotify-client
TryExec=spotify
Exec=env LD_PRELOAD=/usr/local/lib/spotify-adblock.so spotify %U
Terminal=false
MimeType=x-scheme-handler/spotify;
Categories=Audio;Music;Player;AudioVideo;
StartupWMClass=spotify
```
  </p>
</details>

<details>
  <summary>Flatpak</summary>
  <p>

```
[Desktop Entry]
Type=Application
Name=Spotify (adblock)
GenericName=Music Player
Icon=com.spotify.Client
Exec=flatpak run --file-forwarding --command=sh com.spotify.Client -c 'eval "$(sed s#LD_PRELOAD=#LD_PRELOAD=$HOME/.spotify-adblock/spotify-adblock.so:#g /app/bin/spotify)"' @@u %U @@
Terminal=false
MimeType=x-scheme-handler/spotify;
Categories=Audio;Music;Player;AudioVideo;
StartupWMClass=spotify
```
  </p>
</details>

## Uninstall
```bash
$ sudo make uninstall
```

#### Flatpak
```bash
$ rm -r ~/.spotify-adblock ~/.config/spotify-adblock
$ flatpak override --user --reset com.spotify.Client
```

## Configuration
The allowlist and denylist can be configured in a config file located at (in descending order of precedence):
* `config.toml` in the working directory
* `$XDG_CONFIG_HOME/spotify-adblock/config.toml`
* `~/.config/spotify-adblock/config.toml`
* `/etc/spotify-adblock/config.toml` *(default)*
