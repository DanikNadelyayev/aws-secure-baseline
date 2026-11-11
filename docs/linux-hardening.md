# Linux hardening (Ubuntu 24.04 LTS)

Lab VM (Multipass, ARM64). Measured with Lynis 3.0.9.

| | Hardening index |
|---|---|
| Before | 64 |
| After | 73 |

## What I changed
- Applied all pending updates (incl. 24 security updates) and rebooted into the new kernel.
- Firewall (ufw): default deny incoming, allow only SSH (22/tcp).
- fail2ban enabled for sshd (brute-force protection).
- auditd enabled (audit trail).
- SSH: root login disabled, password auth disabled (keys only), MaxAuthTries 3, LoginGraceTime 30, X11 forwarding off, idle timeout.
- Password policy: PASS_MAX_DAYS 90, PASS_MIN_DAYS 1, default umask 027.
- Core dumps disabled (limits.conf, fs.suid_dumpable=0).
- Installed libpam-pwquality, libpam-tmpdir, debsums.

## What I deliberately skipped
Separate partitions for /home, /tmp, /var and a GRUB password: relevant for physical servers, not for a throwaway VM. Password hashing rounds: Ubuntu 24.04 already uses yescrypt.
