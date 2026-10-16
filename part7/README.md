# Part 7 - TNFS servers

Video: https://youtu.be/Zf3tIFPBSMc

This part shows three TNFS servers for FujiNet:

1. **tnfsd** shares a folder from your computer. [Part 6](../part6/) uses this server.
2. **TNFSD** runs on CP/M. One CP/M machine makes its disk available to another.
3. **de-tnfsd** is for sharing files with other people. It has a download folder and a separate
   upload folder.

## 1. Install tnfsd (Terminal, one time only)

Download tnfsd for your computer from the
[tnfsd releases](https://github.com/FujiNetWIFI/tnfsd/releases). On a Mac, Safari unpacks it,
and the program is in Downloads. If you get a zip file, double-click it first.

Make a folder for the program and a separate folder for the files that you share. Then move the
program into the first folder.

```
mkdir ~/tnfs ~/tnfsroot
mv ~/Downloads/tnfsd ~/tnfs/
cd ~/tnfs
xattr -dr com.apple.quarantine ./tnfsd
ln -sf ~/tnfs/tnfsd ~/.local/bin/tnfsd
```

The `xattr` line tells macOS that the program is safe to run. The link puts `tnfsd` in
`~/.local/bin`, next to the `claude` command from [part 2](https://youtu.be/rgRs1Zv-OPg). Then you
can start `tnfsd` from any folder.

Windows and Linux: see the FujiNet wiki page
[Setting up a TNFS Server](https://github.com/FujiNetWIFI/fujinet-firmware/wiki/Setting-up-a-TNFS-Server).

## 2. Start tnfsd (Terminal)

Start the server and give it the folder to share. The server writes a log entry for each request,
so send its output to a file:

```
tnfsd ~/tnfsroot > ~/tnfs/tnfsd.log 2>&1
```

The window shows nothing, but the server runs. Keep this window open while you use the server.
Press Control-C to stop the server.

Each start makes a new log file. The log grows only while the server runs. Remember this if the
server runs for a long time. The first lines of the log show the version, the port and the shared
folder:

```
head -3 ~/tnfs/tnfsd.log
```

## Good to know

- To start the server when you log in, see the FujiNet wiki page
  [Setting up a TNFS Server](https://github.com/FujiNetWIFI/fujinet-firmware/wiki/Setting-up-a-TNFS-Server).
- If the firewall of your computer is on, allow inbound connections to `tnfsd`.
- Use upper-case file names in the shared folder. CP/M sends every name in upper case.

## 3. TNFSD: a TNFS server on CP/M

[TNFSD](https://github.com/trgeuy/fujinet-cpm22/tree/main/cpm-tnfsd) is part of the FujiNet CP/M
tools. Read its limits before you use it:

- The machine that runs TNFSD does nothing else while TNFSD runs.
- TNFSD serves the current drive and user area only.
- TNFSD accepts one client connection at a time.

In the video, the real Altair runs the server, and an altairsim machine is the client.

On the server machine (CP/M), start TNFSD. It shows the IP address of its FujiNet adapter. Clients
connect to this address.

```
TNFSD
```

On the client machine (CP/M), put that address in place of `<address>`:

```
FUJIDIR N1:TNFS://<address>/*.TXT
FUJIGET N1:TNFS://<address>/COPY.TXT COPY.TXT
TYPE COPY.TXT
FUJIPUT HELLO.TXT N1:TNFS://<address>/HELLO.TXT T
```

TNFSD shows each request on the console of the server machine. A `not found` line for
`HELLO.TXT` is normal: FUJIPUT first makes sure that the file name is free.

Press Control-C on the server machine to stop TNFSD.

## 4. de-tnfsd: a server for other people

[de-tnfsd](https://github.com/deltecent/de-tnfsd) works like an old anonymous FTP site:

- Anyone can download files from the folder `PUB`, but nobody can change that folder.
- Uploads go into the folder `INCOMING`. Nobody can list or download the files in that folder.

The administrator of the server checks each upload. The administrator moves approved uploads
into `PUB`.

To set up this server, see
[tnfsd-server-setup](https://github.com/trgeuy/fujinet-cpm22/tree/main/tnfsd-server-setup) in
the FujiNet CP/M tools.

## Which server to use

| You want to | Use |
|---|---|
| Move files between your own systems | tnfsd on your computer |
| Copy files between two CP/M machines, with no other computer | TNFSD |
| Share files with other people | de-tnfsd |
