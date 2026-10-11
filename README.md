# Dots and Boxes

A terminal version of the classic Dots and Boxes game written in C for CMPS 241 (Systems Programming) at AUB. It supports local two-player games, a minimax bot, and online play over TCP. It can also be set up to boot straight into the game on Alpine Linux.

## Features

- **4×5 board** (5×6 dots) drawn in the terminal, with each box showing its owner (`A` or `B`)
- **Local mode:** two players on one keyboard, or one player against the bot
- **Bot:** minimax with alpha-beta pruning and iterative deepening. It searches 5 to 8 moves deep depending on how many moves remain, tries box-completing moves first, and follows the extra-turn rule
- **Online mode:** an authoritative TCP server that checks every move, keeps score, and passes moves between two clients
- **Multithreading:** the server runs one `pthread` per client, and each client has a background thread for receiving messages. Both sides synchronize with mutexes and condition variables
- **Boot to game:** on Alpine Linux the game takes the place of the login prompt on `tty1`

## Requirements

GCC, Make, and POSIX threads. Any Linux distribution works. On Alpine, install `build-base`.

## Build and run

```sh
make          # builds ./dots_and_boxes
make run      # builds and runs the game
make clean    # removes object files and the binary
```

## Alpine Linux VM setup (launch on boot)

1. Install Alpine in a VM (`setup-alpine`) and log in as `root`.
2. Get the code:
   ```sh
   apk add git
   git clone <repo-url>
   cd <repo-folder>
   ```
3. Run the setup script, then reboot:
   ```sh
   sh setup.sh
   reboot
   ```

`setup.sh` installs the toolchain, builds the game, and points `tty1` in `/etc/inittab` at the game binary. After the reboot, the VM opens directly into the game with no login, and the game restarts whenever it exits.

## How to play

When the game starts, choose **1** for local or **2** for online.

To make a move, enter the row and column of two adjacent dots as `r1 c1 r2 c2`. For example, `0 4 0 3` draws the horizontal line between dots (0,4) and (0,3). If your line completes a box, you claim it and play again. The game ends once all 20 boxes are claimed.

### Playing online

1. On the host machine, start the game and choose **2** (online), then **1** (host). You can keep the default port 9090 or enter a different one.
2. Each player starts the game and chooses **2**, then **2** (join), and enters the host's IP address and port. A player on the same machine as the server can use `127.0.0.1`.
3. The first player to connect is A and moves first.
