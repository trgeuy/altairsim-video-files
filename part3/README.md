# Part 3 - Setting up a project folder

Video: https://youtu.be/KmunDO4-VrU

## 1. Install the developer tools (Terminal, one time only)

```
xcode-select --install
```

macOS asks if you want to install the command line developer tools. Click **Install**. This gives
you `git`, Python and a compiler.

Windows and Linux: Claude Code's [setup page](https://code.claude.com/docs/en/setup) says what it needs
on your system. On Windows, see [Set up on Windows](https://code.claude.com/docs/en/setup#set-up-on-windows)
(Git for Windows). On Linux, install git from your package manager:
[git-scm.com/install/linux](https://git-scm.com/install/linux).

Optional: [Homebrew](https://brew.sh) installs more tools with one command. For example,
`brew install poppler` gives you `pdftotext`, which converts PDF files to text.

## 2. Make the project folder (Terminal)

```
mkdir ~/altair-project
cp ~/altairsim/examples/cpm/cpm22-buffered.toml ~/altairsim/examples/cpm/cpm22b23-56k.dsk ~/altair-project/
cd ~/altair-project
mkdir spare
cp cpm22-buffered.toml cpm22b23-56k.dsk spare/
claude mcp add altairsim --scope project -- altairsim cpm22-buffered.toml --mcp
mkdir Documentation Reference
```

Copy only the files that the project needs: the machine file and its disk.

Put your manuals (PDF) in `Documentation`.

## 3. Prompts (in Claude)

Each prompt is also a file in [prompts/](prompts/), so that you can copy it in one piece.

**Convert the manuals.** Change the chapter names to the ones that your project needs.
([convert-manuals.txt](prompts/convert-manuals.txt))

```
Convert chapter 1 of the CP/M manual, and chapter 1 and appendix D of the BASIC-80 manual, from Documentation to Markdown files in Reference. Fix obvious OCR errors.
```

**The program in the video.** ([fib-bas.txt](prompts/fib-bas.txt))

```
Write a BASIC program called FIB.BAS that prints the Fibonacci numbers up to 100. Run it in MBASIC on the Altair, and save it on the Altair's disk.
```

**The start-up and close-out checklists.** ([claude-md.txt](prompts/claude-md.txt))

```
Write a CLAUDE.md for this project. Put two checklists in it. Don't write WHERE-WE-LEFT-OFF.md yet.
Start-up, when I say "Let's start": read WHERE-WE-LEFT-OFF.md, look at what is in the Reference folder (read a file there only when a task needs it), boot the machine, and tell me where we stopped and what the next step is.
Close-out, when I say "We're done for today": stop anything that still runs, check that the work is saved on the Altair's disk, and write what you learned and where we are in WHERE-WE-LEFT-OFF.md. Keep only the latest session in it: move the older entries to WHERE-WE-LEFT-OFF-ARCHIVE.md, so we can always look back. Tell me when it's saved.
```

After that, start each session with `Let's start.` and end it with `We're done for today.`

**The change in the second session.** ([fib-input.txt](prompts/fib-input.txt))

```
Change FIB.BAS so it asks for the upper limit with INPUT instead of always using 100. Run it with 1000, and save it.
```

**Add a task to a checklist.** An example: ([startup-task.txt](prompts/startup-task.txt))

```
Add to the start-up checklist: check Documentation for new files, and ask me before you convert one.
```

## 4. Start again from a known disk (Terminal, with Claude stopped)

```
cd ~/altair-project
cp spare/* .
```

## Bonus: a status line that shows your usage

([statusline.txt](prompts/statusline.txt))

```
/statusline Show the model, the effort level, how much of the context window is used, how much of the 5-hour and weekly usage is left, and when the 5-hour window resets. Color each number green, then yellow, then red as it runs low.
```

This changes a setting for all your projects, not only this one.
