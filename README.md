# systemd Service Template

Hardened service and timer examples for long-running applications and scheduled jobs.

```bash
sudo install -m 0644 units/example@.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now example@production
systemctl status example@production
journalctl -u example@production
```

Environment files contain configuration only. Credentials should be loaded from a protected secret store.
