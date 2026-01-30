# ZHA Quirk Fix for Tuya TS0014 4-Gang Switch

Fixes the Tuya TS0014 (e.g. Lerlink `_TZ3000_sovlwiix`) in Home Assistant's ZHA so each gang gets its own switch entity.

## TL;DR

SSH into Home Assistant and run:

```bash
mkdir -p /config/custom_zha_quirks/ts0014
cd /config/custom_zha_quirks/ts0014
wget https://raw.githubusercontent.com/ksaitor/TS0014-lerlink-4g-fix/master/ts0014.py
wget https://raw.githubusercontent.com/ksaitor/TS0014-lerlink-4g-fix/master/__init__.py
```

Add to `configuration.yaml`:

```yaml
zha:
  custom_quirks_path: /config/custom_zha_quirks
```

Restart HA. Remove and re-pair the device if needed.

## Verify

On the ZHA device info page you should see `quirk_applied: True` and 4 separate switch entities.

## Details

Without this quirk ZHA shows `quirk_applied=False` and all 4 gangs behave as one switch. The quirk replaces the standard `OnOff` cluster with `TuyaZBOnOffAttributeCluster` on each endpoint so they work independently.

The file includes multiple signature variants for different TS0014 hardware revisions. ZHA tries each until one matches your device.

## License

MIT
