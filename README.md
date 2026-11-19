# tmp-autoclean

Every 30 minutes, the timer looks for top-level `/tmp` entries owned by the user
running it. When `/tmp` reaches 50% full, it deletes entries that have gone
12 hours without a modification, metadata change, or read and are unused by
running processes. It deletes the oldest first until usage drops below 35%.
Directory access times are ignored.

The script skips sockets, trees containing sockets
or files owned by another user, and reserved name prefixes listed in `SKIP_PREFIXES`
(including `claude-`, `systemd-private-`, `tmux-` and `ssh-`). Process-use checks
are limited to what the invoking user can read under `/proc`.

To install, run from the checkout:

```bash
mkdir -p ~/.local/bin ~/.config/systemd/user
install -m 755 tmp-autoclean ~/.local/bin/
cp tmp-autoclean.{service,timer} ~/.config/systemd/user/
systemctl --user daemon-reload && systemctl --user enable --now tmp-autoclean.timer
```

To disable cleanup, run `systemctl --user disable --now tmp-autoclean.timer`.
To preview, run `tmp-autoclean --dry-run --force`. It ignores fullness thresholds but keeps
the age and in-use checks. It reports every eligible entry in the totals and
prints individual paths only for entries of at least 50 MiB. Tune with
`TMP_AUTOCLEAN_AGE_HOURS`, `TMP_AUTOCLEAN_START_PCT` and `TMP_AUTOCLEAN_STOP_PCT`.
