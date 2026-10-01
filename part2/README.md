# Part 2 - Installing Claude Code and connecting it to altairsim

Video: https://youtu.be/fJCYAMyUkCo

You need a paid Claude plan (Pro or higher).

## Install Claude Code (Terminal)

```
curl -fsSL https://claude.ai/install.sh | bash
```

If the installer says that `~/.local/bin` is not in your PATH, run the command that it shows you.
On the Mac in the video it was:

```
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
claude --version
```

For other systems, see https://code.claude.com/docs/en/setup.

## Make a folder to try things in

```
cd ~/altairsim/examples/ai-mcp
cp -R . ~/altair-ai
cd ~/altair-ai
```

## Install the altairsim skill

```
mkdir -p ~/.claude/skills
cp -R ~/altairsim/skills/altairsim ~/.claude/skills/
```

After you update altairsim (see [part 1](../part1/)), copy the new skill too:

```
rm -rf ~/.claude/skills/altairsim
cp -R ~/altairsim/skills/altairsim ~/.claude/skills/
```

Other AI applications that do not read skills: give them `DRIVING-WITH-AI.md`, which is in your
`~/altairsim` folder.

## Connect altairsim to Claude Code

```
claude mcp add altairsim --scope project -- altairsim cpm-ai.toml --mcp
claude mcp list
claude
```

The first time, Claude Code asks two questions:

1. Do you trust this folder? The answer already selected is **No, exit**. Choose
   **Yes, I trust this folder**.
2. Use the altairsim MCP server? Choose **Use this MCP server**.

## The prompt from the video

In [hello-prompt.txt](hello-prompt.txt):

```
Using the altairsim MCP tools, boot the CP/M machine and assemble and run HELLO.ASM off its disk. It should print HELLO, WORLD. If it doesn't, debug it in the simulator and fix the source.
```
