# Updating the client

The client checks for a newer release when it starts, and again whenever Inferoute answers a request. It does not replace itself while it is running.

## When you see "Update available"

The console, the local dashboard, and the Windows tray icon show a line like this:

```text
Update available: 1.2.0 -> 1.4.0. Run 'inferoute-client update' then restart.
```

The client keeps working. Update when you can.

1. Stop the client.
2. Run `inferoute-client update`.
3. Start the client again.

`inferoute-client update --check` only prints whether a newer release exists.

## When you see "Update required"

Inferoute has stopped accepting that release. The client prints the message and exits, and a running client that falls behind slows its health reports and shows the same line in red.

Stop the client, run `inferoute-client update`, and start it again.

A release stays accepted for **90 days after the next release is published**.

## What the update command does

`inferoute-client update` downloads the zip for your system from the current Inferoute release, checks it against `checksums.txt`, and replaces the binary on disk. It refuses to run while the client is still running, so stop it first.

On Windows the previous `.exe` is renamed to `inferoute-client.exe.old` and removed the next time the new client starts.

## Docker

The image does not update its own binary. Pull the new image and restart the container. For example, with Compose:

```yaml
restart: unless-stopped
```

Then pull `inferoute/inferoute-client:latest` and recreate the container.

## Linux and macOS install script

`scripts/install.sh` installs the newest release and checks `checksums.txt` when that file is on the release. The same check runs in the Windows install script.
