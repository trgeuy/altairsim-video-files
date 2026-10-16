# Part 6 - Moving files between systems with FujiNet

Video: https://youtu.be/vZJVMi5UcAw

FujiNet moves files between altairsim machines and real Altairs over the network. The video uses
the [FujiNet CP/M tools](https://github.com/trgeuy/fujinet-cpm22): FUJIDIR, FUJIGET and FUJIPUT.

If you use only altairsim, you do not need FujiNet. The host bridge commands `R` and `W` copy
files between your computer and the simulated machine.

## Before you start

- Install altairsim ([part 1](https://youtu.be/4fUiHYfFbdo)).
- Start a TNFS server. The video uses tnfsd on the same computer, with the shared folder
  `~/tnfsroot`. [Part 7](../part7/) shows how to install it.
- Put a text file in the shared folder. The video uses `HELLO.TXT`.
- Use upper-case names for all files in the shared folder. CP/M changes the command line to
  upper case, so it cannot get a file with a lower-case name.

## 1. Install FujiNet-PC (Terminal, one time only)

FujiNet-PC is a program that works like a FujiNet adapter for altairsim machines. Download the
RS-232 version for your computer from the
[FujiNet firmware releases](https://github.com/FujiNetWIFI/fujinet-firmware/releases). The file
name starts with `fujinet-pc-RS232`.

On macOS, FujiNet-PC needs OpenSSL. Install it with [Homebrew](https://brew.sh):

```
brew install openssl@4
```

Make a folder for FujiNet-PC and unpack the download into it. The `xattr` line tells macOS that
the program is safe to run.

```
mkdir -p ~/fujinet-pc
tar xf ~/Downloads/fujinet-pc-RS232_*.tar* -C ~/fujinet-pc
cd ~/fujinet-pc/fujinet-pc-RS232
xattr -dr com.apple.quarantine .
```

Windows and Linux: see the [FujiNet-PC README](https://github.com/FujiNetWIFI/fujinet-pc).

## 2. Start FujiNet-PC (Terminal)

Start FujiNet-PC in its own terminal window. It is ready when it shows
`BoIPChannel accepting connections`. Keep this window open.

```
cd ~/fujinet-pc/fujinet-pc-RS232
./run-fujinet -u 127.0.0.1:8005
```

Start FujiNet-PC before you start the altairsim machine that uses it.

## 3. Make the project folder (Terminal)

Download the FujiNet CP/M tools from the
[releases page](https://github.com/trgeuy/fujinet-cpm22/releases). Safari unzips them in
Downloads. Move them to your home folder, then make the project folder.

```
mv ~/Downloads/FujiNet-CPM-Tools ~/
mkdir ~/fujinet-project
cp ~/altairsim/examples/cpm/cpm22-buffered.toml ~/altairsim/examples/cpm/cpm22b23-56k.dsk ~/fujinet-project/
cd ~/fujinet-project
cp ~/FujiNet-CPM-Tools/FUJI*.COM .
open -e cpm22-buffered.toml
```

The example machine does not connect its second serial port to anything. Go to the end of the
file in TextEdit and paste these four lines. They connect the second serial port of the 2SIO to
FujiNet-PC.

```
[[board]]
id = "sio0"
  [board.unit.b]
  connect = "socket:localhost:1985"
```

Paste the lines. Do not type them. TextEdit changes the first letter of a typed line to upper
case, and then the machine file does not load. Save the file.

Make a backup copy of the machine file and its disk, then start the machine:

```
mkdir spare
cp cpm22-buffered.toml cpm22b23-56k.dsk spare/
altairsim cpm22-buffered.toml
```

## 4. Use the tools in altairsim (CP/M)

The tools are not on the CP/M disk yet. At the `A>` prompt, copy them with the host bridge
command `R`:

```
R FUJIDIR.COM
R FUJIGET.COM
R FUJIPUT.COM
```

Use the IP address of your computer for the server, from altairsim and from a real machine. On
macOS, find it in System Settings > Network. Put it in place of `<address>` below.

```
FUJIDIR N1:TNFS://<address>/
FUJIGET N1:TNFS://<address>/HELLO.TXT HELLO.TXT
TYPE HELLO.TXT
FUJIPUT HELLO.TXT N1:TNFS://<address>/COPY.TXT T
FUJIDIR N1:TNFS://<address>/
```

The `T` at the end of the FUJIPUT command tells FUJIPUT that the file is a text file.

## 5. Get the files onto a real Altair

A real machine needs a FujiNet for RS-232 adapter. The video uses one from
[FutureVision Research](https://www.fvresearch.com/product/fujinet-rs232/). To set it up, see
[the adapter guide](https://github.com/trgeuy/fujinet-cpm22/tree/main/fujinet-rs232).

The tools, as you download them, use port B of a MITS 88-2SIO serial board at I/O ports 10 to 13
hex. For a different serial board or different ports, see
[adapting the tools to your hardware](https://github.com/trgeuy/fujinet-cpm22/blob/main/docs/adapting-to-hardware.md).

The real Altair in the video already has FUJIGET from
[part 5](https://youtu.be/mNeaQOBk0RY). Copy the other two tools into the shared folder of the
server (Terminal):

```
cp ~/FujiNet-CPM-Tools/FUJIDIR.COM ~/FujiNet-CPM-Tools/FUJIPUT.COM ~/tnfsroot/
```

Connect to the console of the Altair. The video uses `screen`. Use the port name and the baud rate
of your machine:

```
screen /dev/cu.usbserial-FTAJCNNY0 9600
```

On the Altair (CP/M):

```
DIR FUJI*.*
FUJIGET N1:TNFS://<address>/FUJIDIR.COM FUJIDIR.COM
FUJIGET N1:TNFS://<address>/FUJIPUT.COM FUJIPUT.COM
FUJIDIR N1:TNFS://<address>/
FUJIGET N1:TNFS://<address>/COPY.TXT COPY.TXT
TYPE COPY.TXT
```

`COPY.TXT` came from the altairsim machine, through the server, onto the real Altair.

To leave `screen`, press Control-A, then K.

## More

[WGET](https://github.com/trgeuy/fujinet-cpm22/tree/main/wget) downloads files from websites
directly onto the CP/M disk.
