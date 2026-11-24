# nmap_oui_update

Updates nmap's MAC address OUI (vendor prefix) file with the latest data
from the IEEE registry, so `nmap -sn`/`-A` scans can resolve more devices
to their manufacturer.

It downloads the current OUI list from IEEE, compares it against the OUI
entries nmap already ships, appends any new ones, and installs the result
over nmap's file — after saving a timestamped backup of the original.

## Requirements

- Python 3
- [`requests`](https://pypi.org/project/requests/) (`pip install requests`)
- Linux with nmap installed (expects `/usr/share/nmap/nmap-mac-prefixes`)
- root privileges (writes into `/usr/share/nmap/` and reads the existing file)

## Usage

```
sudo python3 ./nmap_oui_update.py
```

## What it does

1. Backs up the current `/usr/share/nmap/nmap-mac-prefixes` to
   `backups/nmap-mac-prefixes.<timestamp>` (created next to the script).
2. Downloads the latest OUI data from IEEE to `ieee_oui_data.txt`.
3. Parses out any OUIs missing from the existing nmap file and appends them,
   writing the merged result to `nmap-mac-prefixes_updated`.
4. If new records were found, copies `nmap-mac-prefixes_updated` over
   `/usr/share/nmap/nmap-mac-prefixes`.

If no new OUIs are found, the nmap file is left untouched.

## Exit codes

| Code | Meaning |
|------|---------|
| 0 | Success (update applied, or no new records found) |
| 1 | Not running as root |
| 2 | Failed to download OUI data from IEEE |
| 4 | Failed to copy the updated file into `/usr/share/nmap/` |

## Credits

Jeff Gregor, based on code by Travis Phillips.

<!-- /doc-it audited: 2026-11-24 -->
