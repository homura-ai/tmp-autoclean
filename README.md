# tmp-autoclean

pbox's /tmp is a 16G tmpfs with a per-user quota. Every 10 min this deletes
top-level /tmp entries owned by vi that nothing has touched for 12h (3h once
/tmp is 80% full) and no running process uses (cwd/exe/open/mapped files or a path on its command line),
oldest first, but only when /tmp is at least 50% full, stopping at 35%. "Full"
is the higher of the filesystem's fill and vi's quota fill, because the quota
can run out while the filesystem still has room. Fedora's own tmpfiles cleanup only
removes things after 10 days.

Install: `install -m 755 tmp-autoclean ~/.local/bin/ && cp tmp-autoclean.{service,timer} ~/.config/systemd/user/ && systemctl --user daemon-reload && systemctl --user enable --now tmp-autoclean.timer`
Rollback: `systemctl --user disable --now tmp-autoclean.timer`
Preview: `tmp-autoclean --dry-run --force`. Tune with TMP_AUTOCLEAN_AGE_HOURS / _START_PCT / _STOP_PCT / _URGENT_PCT / _URGENT_AGE_HOURS.
