# `snapshot`

*An opinionated, bare-metal Bash tool to synchronize working directory snapshots with a remote FTPES server.*

Designed for macOS and UNIX distributions, it provides a straightforward way to create, upload, and download snapshots of any working directory with minimal hassle.

Just `cd` into the directory you want to snapshot, and run `snapshot push` or `snapshot pull` to manage your snapshots.

`snapshot` automates the process of zipping/unzipping archives of directories, and downloading/uploading them to/from a remote FTP server, ultimately saving thousands of mouse clicks and keystrokes.

- Straightforward usage with simple commands.
- Super lightweight, about 7KB in size.
- Minimal dependencies by using only `zip`, `unzip`, `find` and `curl`.
- Supports weird FTPES servers using TLS v1.2.
- Human-friendly info and error messages.
- Configure it once and use it anywhere.

>**Bonus**: When the tool detects that the directory is a Flutter project, and if the `flutter` command is available, it automatically runs `flutter clean` before packing the snapshot. This ensures that snapshots of such projects are free of unnecessary build artifacts, to optimize storage space and network transfer.

### Why?

The initial motiviation for this tool was to *avoid committing ugly work-in-progress code to Git*, especially when working on multiple machines simultaneously. It allows to quickly snapshot your current working directory and push it to a remote server, ensuring you can always retrieve your latest work without cluttering your Git history — **all in a few seconds**.

However, this tool is useful in many other power-user scenarios, such as:

- **Backing up** music, photos, etc.
- **Avoid relying on cloud services** to keep stuff synchronized across devices.
- **Archiving projects** before making significant changes.
- **Archiving old projects** to free up space on your local machine.

<details>

<summary>Want a little anecdote?</summary>

At first, it was a simple script embedded in every project, with the same configuration file duplicated all over the place. 

But as more projects were created, it became cumbersome to maintain. This motivated the decision to extract it into a standalone tool.

I felt it took too long to manually zip and unzip, drag and drop using an FTP client or a cloud service UI to synchronize directories across devices. It's much faster and efficient to just run a one-liner in the terminal, and this tool makes it easy to do so.
</details>

## Usage

`snapshot [pack | push | pull] [-q | --quiet]`

**Important**: All of these commands must be invoked from the directory you want to snapshot — ie. when in `my_project`, running these commands will assume it manages snapshot of the `my_project` directory.

### Commands

- **`pack`**: Creates a snapshot of the current working directory. A snapshot is a ZIP archive containing the current state of the directory. 
  The snapshot is named with the directory name, and the archive's root is a directory with the same name containing the files.

- **`push`**: Packs a snapshot and uploads it to the remote FTP server. Note that this command calls `pack` first, so you don't have to run it beforehand.

- **`pull`**: Downloads the latest snapshot from the remote FTP server to the current working directory, and replaces the current contents with the snapshot's contents. 
  
  **Note**: If the local directory is not empty, it will be replaced with the snapshot's contents, so make sure to back up any important files before running this command.

- **`-q` or `--quiet`**: Suppresses or reduces output messages, useful for scripting or when you don't need detailed feedback.

### Examples

First, navigate to the directory where you want to snapshot your work:

```bash
cd ~/Documents/Work/my_project
```

To take a snapshot of the current working directory and send it to the remote server, simply run:

```bash
snapshot push
```

This is typically used when you want to save the contents of your current working directory to the remote server, ensuring you can safely retrieve it later from (almost) anywhere in the world.

Later on, to download the latest snapshot from the remote server and replace the current working directory's contents, run:

```bash
snapshot pull
```

## Installation

1. Move the `snapshot` executable to `usr/local/bin`, and make it executable:

   ```bash
   mv snapshot /usr/local/bin/snapshot
   chmod +x /usr/local/bin/snapshot
   ```

2. Create your user configuration file at `~/.snapshot/config`:

   ```bash
   mkdir -p ~/.snapshot
   nano ~/.snapshot/config
   chmod 600 ~/.snapshot/config
   ```

3. Edit the configuration file with your FTP server credentials:

   ```bash
   REMOTE_HOST="your.ftp.server"
   REMOTE_PORT="21"  # Default FTP port
   REMOTE_USER="your_username"
   REMOTE_PASS="bmljZSB0cnksIGRpZGR5"  # base64 encoded
   REMOTE_PATH="/path/to/remote/directory"
   ```

## Ideas

- [ ] Add an interactive credentials provisioning flow when the tool runs for the first time.

- [ ] Add FTP connection parameters such as TLS version and security protocol to the configuration file, to override the default hardcoded values.

- [ ] Add a confirmation when pushing to the remote server, showing the last time the remote snapshot was updated.

- [ ] Add a confirmation when pulling from the remote server, showing the last time the local snapshot was updated.

- Add more options:

	- [ ] `check` to print the local working directory's snapshot name, whether the remote snapshot exists, and the **last time it was updated** — both locally and remotely. This would be uselful to allow mixups when frequently pushing and pulling snapshots.

	- [ ] `--remote-subpath` to override or `--remote-path` to supplement the `REMOTE_PATH` variable in the config file, allowing to push/pull snapshots to/from a subdirectory of the remote path.

	- [ ] `--local-subpath` to override or `--local-path` to supplement the local path, allowing to push/pull snapshots to/from a subdirectory of the current working directory.

## Side notes

Some tweaking may be needed for the `curl` request to work with your FTP server, especially if it requires specific TLS versions or security protocol, or if it has unique authentication methods. 

The script is designed to work with a single FTPES server, which has very specific requirements. It is easily adaptable to other FTP servers with minor modifications.

Made with ♥ by [mrcendre](https://cendre.me).

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.