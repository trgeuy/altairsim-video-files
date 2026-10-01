# Part 1 - Installing and running altairsim

Video: https://youtu.be/VgnYONLjnp0

## Install (Terminal)

Download `altairsim-1.2.0-macos-arm64.tar.gz` from https://altairsim.com first. Then:

```
cd ~/Downloads
tar xzf altairsim-1.2.0-macos-arm64.tar.gz
mv altairsim-1.2.0-macos-arm64 ~/altairsim
cd ~/altairsim
xattr -dr com.apple.quarantine ./altairsim
```

Make the `altairsim` command work from any folder:

```
sudo mkdir -p /usr/local/bin
sudo ln -sf ~/altairsim/altairsim /usr/local/bin/altairsim
altairsim --version
```

## Run the CP/M example

```
cp -R ~/altairsim/examples/cpm ~/altair-cpm
cd ~/altair-cpm
altairsim cpm22-buffered.toml
```

At the `A>` prompt, type `DIR` to see what is on the disk.

## Update altairsim to a newer version

Your own work is in copies (for example `~/altair-cpm`), so an update does not change it.

1. Download the new release from https://altairsim.com.
2. Keep the old version as a backup and put the new one in its place. Change `X.Y.Z` to the new
   version number:

   ```
   cd ~/Downloads
   mv ~/altairsim ~/altairsim-old
   tar xzf altairsim-X.Y.Z-macos-arm64.tar.gz
   mv altairsim-X.Y.Z-macos-arm64 ~/altairsim
   xattr -dr com.apple.quarantine ~/altairsim/altairsim
   ```

3. Check it: `altairsim --version`

The link in `/usr/local/bin` points to `~/altairsim/altairsim`, so you do not need `sudo` again.
When the new version works, you can delete `~/altairsim-old`.

If you use Claude (part 2), copy the new skill too. The commands are in [part 2](../part2/).
