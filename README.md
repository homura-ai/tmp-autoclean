# tmp-autoclean

pbox's /tmp is a 16G tmpfs with a per-user quota. Every 30 min this deletes
top-level /tmp entries owned by vi whose contents have not been modified, changed
or read for 12h (directory access times are ignored) and no visible running
process uses (cwd/root/exe/open/mapped files), oldest first, but only when
/tmp is at least 50% full, stopping at 35%. Fedora's own tmpfiles cleanup only
removes things after 10 days.

The script uses the invoking user's UID. It skips sockets, trees containing sockets
or files owned by another user, and reserved name prefixes listed in `SKIP_PREFIXES`
(including `claude-`, `systemd-private-`, `tmux-` and `ssh-`). Process-use checks
are limited to what the invoking user can read under `/proc`.

Install: `install -m 755 tmp-autoclean ~/.local/bin/ && cp tmp-autoclean.{service,timer} ~/.config/systemd/user/ && systemctl --user daemon-reload && systemctl --user enable --now tmp-autoclean.timer`
Rollback: `systemctl --user disable --now tmp-autoclean.timer`
Preview: `tmp-autoclean --dry-run --force` ignores fullness thresholds but keeps
the age and in-use checks. It reports every eligible entry in the totals and
prints individual paths only for entries of at least 50 MiB. Tune with
`TMP_AUTOCLEAN_AGE_HOURS`, `TMP_AUTOCLEAN_START_PCT` and `TMP_AUTOCLEAN_STOP_PCT`.
