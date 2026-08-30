# systemd Service Template

Hardened examples for a long-running instance service, a scheduled maintenance job and log rotation.

## Files

- `example@.service`: instance unit such as development or production
- `maintenance.service`: oneshot task
- `maintenance.timer`: persistent daily schedule with randomized delay
- `logrotate/example`: retention and safe reopen signal

## Install

```bash
sudo useradd --system --home /var/lib/example --create-home --shell /usr/sbin/nologin example
sudo install -d -o example -g example /var/lib/example
sudo install -d /etc/example
sudo install -m 0644 units/example@.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now example@production.service
```

Create `/etc/example/production.env` and `production.yaml` with permissions appropriate to their contents.

## Operate

```bash
systemctl status example@production
journalctl -u example@production -f
systemctl show example@production -p MainPID -p NRestarts
sudo systemctl reload-or-restart example@production
```

## Timer example

```bash
sudo install -m 0644 units/maintenance.* /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now maintenance.timer
systemctl list-timers maintenance.timer
systemctl status maintenance.service
```

`Persistent=true` runs a missed job after the host returns. Randomized delay prevents every server from starting maintenance simultaneously.

## Security choices

The service cannot gain privileges, receives a private temporary directory, has a read-only system view and no Linux capabilities. Only the application data directory is writable.

## Troubleshooting

- exit code 203/EXEC: verify executable path and permissions;
- environment file error: check path and quoting;
- restart loop: inspect `journalctl` and application exit code;
- write denied: add only the required path to `ReadWritePaths`;
- timer not firing: inspect `systemctl list-timers --all` and calendar syntax with `systemd-analyze calendar`.
