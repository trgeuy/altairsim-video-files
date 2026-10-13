# Part 5 - Claude on a real serial line

Video: https://youtu.be/mNeaQOBk0RY

Claude drives a real Altair 8800c over a USB serial adapter, and we watch its console through a
mirror. The video uses the [serial MCP server](https://github.com/trgeuy/serial-mcp-server).
Any machine with a serial console works the same way.

Before you ask Claude to take control, test the connection with your own terminal program if you
can.

## 1. Find the port of your USB serial adapter (Terminal)

With the adapter unplugged, list the serial ports. Plug the adapter in, then list them again. The
new entry is your port.

```
ls /dev/cu.*
```

Adapters with an FTDI chip work as soon as you plug them in. A Prolific (PL2303) adapter shows no
port until you install its driver:

1. Install the PL2303 Serial application from the App Store, and open it once.
2. Open System Settings and search for Extensions. Under Login Items and Extensions, click the i
   next to the driver. Turn on the driver and enter your password.
3. Unplug the adapter and plug it back in.

- Windows: the port is one of your COM ports.
- Linux: the port is usually something like `/dev/ttyUSB0` or `/dev/ttyACM0`.

## 2. Install the serial MCP server (Terminal, one time only)

Install [uv](https://docs.astral.sh/uv) (macOS, with [Homebrew](https://brew.sh)), then the server:

```
brew install uv
uv tool install \
  "serial-mcp-server @ https://github.com/trgeuy/serial-mcp-server/releases/latest/download/serial-mcp-server.tar.gz"
```

Register the server for every project on this computer (`--scope user`), with a read-only mirror:

```
claude mcp add --scope user serial \
  -e SERIAL_MCP_MIRROR=ro \
  -e SERIAL_MCP_MIRROR_TRANSPORT=tcp \
  -- serial_mcp
```

Install the skill, and check that the server is connected:

```
curl -LO https://github.com/trgeuy/serial-mcp-server/releases/latest/download/serial-mcp-skill.zip
unzip -o serial-mcp-skill.zip -d ~/.claude/skills
claude mcp list
```

Windows and Linux: see the
[serial MCP server README](https://github.com/trgeuy/serial-mcp-server).

## 3. Job 1: Claude drives, you watch the mirror

In a second terminal window, start the watch program first. It waits until Claude opens the port.

```
serial-watch
```

Then start `claude` and give it these prompts, one per turn. Change the port name and the serial
settings to the ones for your machine.

1. [run-1-connect.txt](prompts/run-1-connect.txt)
2. [run-2-no-pacing.txt](prompts/run-2-no-pacing.txt) (to see what happens with no pacing)
3. [run-3-find-pacing.txt](prompts/run-3-find-pacing.txt)
4. [run-4-close.txt](prompts/run-4-close.txt)

To see the list of ports, ask `List the serial ports.` on its own. Claude does not know what each
port is connected to, so name the port when you ask Claude to open it.

Claude keeps the pacing values it finds in `~/.config/serial-mcp/pacing-profiles.md`.

Press Control-C in the watch window to stop `serial-watch`.

## 4. Job 2: put a program on the Altair

The video copies `FUJIGET.HEX` (from
[fujinet-cpm22](https://github.com/trgeuy/fujinet-cpm22)) into the project folder first. Part 6
uses this program.

1. [pip-1-copy-fujiget.txt](prompts/pip-1-copy-fujiget.txt)
2. [pip-2-run-fujiget.txt](prompts/pip-2-run-fujiget.txt)

## 5. Type at the Altair together (Terminal, then Claude)

Register a second server with a read-write mirror:

```
claude mcp add --scope user serial-shared \
  -e SERIAL_MCP_MIRROR=rw \
  -e SERIAL_MCP_MIRROR_TRANSPORT=pty \
  -- serial_mcp
```

Ask Claude to use `serial-shared` and give it
[shared-1-connect.txt](prompts/shared-1-connect.txt). The mirror exists only after Claude opens
the port. Then connect to the mirror in a second terminal window:

```
screen /tmp/serial-mcp0
```

Then:

1. [shared-2-dir.txt](prompts/shared-2-dir.txt)
2. Type a command in the `screen` window (the video uses `STAT`).
3. [shared-3-what-did-i-type.txt](prompts/shared-3-what-did-i-type.txt)
4. [shared-4-close.txt](prompts/shared-4-close.txt)

When Claude closes the port, `screen` also exits. To leave `screen` before then, press Control-A,
then K. If Claude reconnects, connect `screen` again.

Windows has no virtual serial ports. Use the TCP read-write mirror instead: see
[Typing along with the agent on Windows](https://github.com/trgeuy/serial-mcp-server#typing-along-with-the-agent-on-windows)
in the serial MCP server README.

Use `serial-shared` only when you both must type at the machine. With the read-only mirror,
anything you type in the mirror window does nothing.
