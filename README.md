# tmp-autoclean

Every 30 minutes, the timer looks for top-level `/tmp` entries owned by the
user running it. It deletes entries that nothing has touched for 12 hours and no
running process uses (cwd/exe/open/mapped files), oldest first, but only when
/tmp is at least 50% full, stopping at 35%. Fedora's own tmpfiles cleanup only
removes things after 10 days.

Install: `install -m 755 tmp-autoclean ~/.local/bin/ && cp tmp-autoclean.{service,timer} ~/.config/systemd/user/ && systemctl --user daemon-reload && systemctl --user enable --now tmp-autoclean.timer`
Rollback: `systemctl --user disable --now tmp-autoclean.timer`
Preview: `tmp-autoclean --dry-run --force`. Tune with TMP_AUTOCLEAN_AGE_HOURS / _START_PCT / _STOP_PCT.
