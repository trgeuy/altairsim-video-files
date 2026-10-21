# Part 8 - A sample project

Video: https://youtu.be/5h-7r_IAMIU

In this part, Claude writes a new CP/M program, FUJITYPE. FUJITYPE prints a text file on the
FujiNet printer. Claude builds and tests the program in altairsim, copies it to a TNFS server, and
then runs it on a real Altair 8800c. This part uses the tools from parts 1 to 7.

## 1. Before the session

The project folder is ready before the session starts, as in [part 3](../part3/):

- An altairsim machine file and its disk. The host bridge tools (R, W) and the FujiNet CP/M tools
  are on the disk.
- A backup copy of the machine file and the disk.
- A `CLAUDE.md` file with the start and close-out checklists from part 3.
- A `Reference` folder with the FujiNet RS-232 User's Manual, two chapters of the CP/M manual
  (converted to Markdown), and the [FujiNet CP/M tools](https://github.com/trgeuy/fujinet-cpm22)
  with their source code.

The manuals are copyrighted, so they are not in this repository. Use your own copies.

## 2. The windows (Terminal)

Open each window before you start Claude. The video uses five windows:

| Window | Command | See |
|---|---|---|
| FujiNet-PC | `./run-fujinet` | [part 6](../part6/) |
| tnfsd | `tnfsd ~/tnfsroot > ~/tnfs/tnfsd.log 2>&1` | [part 7](../part7/) |
| The console mirror of altairsim | `~/altairsim/tools/mirror-watch.sh altairsim` | [part 4](../part4/) |
| The console mirror of the real Altair | `serial-watch` | [part 5](../part5/) |
| Claude | `claude` (in the project folder) | [part 2](../part2/) |

## 3. The prompts (Claude)

Give Claude these prompts, one per turn:

1. [1-start.txt](prompts/1-start.txt)
2. [2-write-fujitype.txt](prompts/2-write-fujitype.txt)
3. Set the printer to Epson 80 on the web page of FujiNet-PC (http://localhost:8000), then
   [3-print-source.txt](prompts/3-print-source.txt)
4. [4-copy-back.txt](prompts/4-copy-back.txt)
5. [5-real-altair.txt](prompts/5-real-altair.txt)
6. [6-close.txt](prompts/6-close.txt)

Prompt 5 uses the TNFS server on your computer (part 7) and the serial MCP server (part 5). Before
you give prompt 5, set the printer of the real FujiNet adapter to Epson 80 on its web page.

## Good to know

- Prompt 2 tells Claude about a known issue in the PDF printers of FujiNet
  ([fujinet-firmware issue #2043](https://github.com/FujiNetWIFI/fujinet-firmware/issues/2043)):
  a line can print below the bottom of the page. The fix
  ([pull request #2070](https://github.com/FujiNetWIFI/fujinet-firmware/pull/2070)) is merged.
  Until a FujiNet release includes it, a program must send one printed line at a time.
- Get each printout from the `/print` page of the FujiNet web page. Each download has the name
  `printout.pdf`. Some browsers replace the previous file with the same name. Rename each
  printout after you download it.
- FUJITYPE and its source code are in the
  [FujiNet CP/M tools](https://github.com/trgeuy/fujinet-cpm22), from
  [release v1.10](https://github.com/trgeuy/fujinet-cpm22/releases/tag/v1.10) on.
