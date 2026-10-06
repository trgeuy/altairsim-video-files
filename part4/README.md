# Part 4 - Watch the Altair's console while Claude works

Video: https://youtu.be/VwTaFkhuo5E

The same job, three ways, on the `basic1` example machine (Altair BASIC 1.0, loaded from tape).
The video uses altairsim 1.3.2.

## 1. Install more tools (Terminal, one time only)

macOS: install [Homebrew](https://brew.sh). Paste the install command from its web page, then run
the three "Next steps" commands that the installer shows, in the order listed. Then:

```
brew install poppler
```

This gives you `pdftotext`, which Claude uses to convert PDF manuals to text.

- Linux: use your package manager. On Debian and Ubuntu, the package is `poppler-utils`.
- Windows: `pdftotext` comes with the Xpdf command line tools: [xpdfreader.com](https://www.xpdfreader.com).

## 2. Make the project folder (Terminal)

The same way as in [part 3](../part3/), with the `basic1` machine:

```
mkdir ~/basic1-project
cd ~/altairsim/examples/basic1
cp basic1.toml LOAD10.HEX "BASIC Ver 1-0.tap" ~/basic1-project/
cd ~/basic1-project
mkdir spare
cp basic1.toml LOAD10.HEX "BASIC Ver 1-0.tap" spare/
mkdir Documentation Reference
```

The tape file name has spaces, so keep the quotes.

Then use the prompts from part 3 to convert your manuals and to write CLAUDE.md. This machine has
no disk, so change the close-out to save the program listing in the project folder.

## 3. Register altairsim with the mirror (Terminal)

The option at the end, `--mirror socket:2323`, is new. If you already registered altairsim in this
folder, remove it first (skip this line in a new folder):

```
claude mcp remove altairsim -s project
```

Then add it with the mirror, and check the result:

```
claude mcp add altairsim --scope project -- altairsim basic1.toml --mcp --mirror socket:2323
cat .mcp.json
```

## 4. Run 1: by hand (Terminal)

```
cd ~/basic1-project
altairsim basic1.toml
```

The tape loads at full speed. To load it at the speed of a real Altair, press Control-E, then:

```
SET cpu0 clock_hz=2000000
SET acr0:tape rate=real
REWIND acr0:tape
RUN 1800
```

The load takes about two and a half minutes. At 100%, press Control-E, then:

```
UNMOUNT acr0:tape
RUN 0
```

Press Return at `MEMSIZ?` and at `WANT SIN-COS-ATN?`. The program in the video:

```
10 FOR I=1 TO 5
20 PRINT I,I*I*I
30 NEXT I
RUN
```

Backspace does not work in BASIC 1.0. To delete a character, type an underscore (`_`).
BASIC 1.0 has no command to exit: press Control-E, then type `QUIT`.

## 5. Run 2: Claude drives (in Claude)

Start `claude` in `~/basic1-project` and say `Let's start.` Then:
([squares-cubes.txt](prompts/squares-cubes.txt))

```
Write a BASIC program that prints the squares and cubes of 1 to 10, and run it.
```

## 6. Run 3: Claude drives, you watch the mirror

In a second terminal window, start the watch script first. It waits until altairsim runs.

```
~/altairsim/tools/mirror-watch.sh altairsim
```

Windows: use `mirror-watch.ps1` in the same `tools` folder. Its first lines show how to run it.

Then start `claude` in `~/basic1-project`, say `Let's start.`, and give it the same prompt as in
run 2. End the session with `We're done for today.` Press Control-C in the watch window to stop
the script.

The watch script only watches: you cannot type into the Altair's console from it.
