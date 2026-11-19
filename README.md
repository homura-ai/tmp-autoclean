# tmp-autoclean

Every 30 minutes, the timer looks for top-level `/tmp` entries owned by the
user running it. It deletes entries that nothing has touched for 12 hours and no
running process uses (cwd/root/exe/open/mapped files). When `/tmp` reaches 50%
full, it deletes the oldest eligible entries first until usage drops below 35%.

To install, run from the checkout:

```bash
mkdir -p ~/.local/bin ~/.config/systemd/user
install -m 755 tmp-autoclean ~/.local/bin/
cp tmp-autoclean.{service,timer} ~/.config/systemd/user/
systemctl --user daemon-reload && systemctl --user enable --now tmp-autoclean.timer
```

To disable cleanup, run `systemctl --user disable --now tmp-autoclean.timer`.
To preview, run `tmp-autoclean --dry-run --force`. Tune with
`TMP_AUTOCLEAN_AGE_HOURS`, `TMP_AUTOCLEAN_START_PCT`, and `TMP_AUTOCLEAN_STOP_PCT`.
