# ZHA Quirk Fix for Tuya TS0014 4-Gang Switch

Fixes the Tuya TS0014 4-gang switch (e.g. Lerlink `_TZ3000_sovlwiix`) in Home Assistant's ZHA integration so that each gang is exposed as a separate switch entity and can be controlled independently.

## The Problem

Without this quirk, ZHA does not recognize the device's endpoint layout and all 4 gangs appear as a single switch, or the device shows `quirk_applied=False` in the ZHA device info.

## Prerequisites

- Home Assistant with the ZHA integration
- SSH or file access to your Home Assistant instance (e.g. via the SSH & Web Terminal add-on, Samba share, or VS Code add-on)

## Installation

### 1. Enable custom ZHA quirks in Home Assistant

Add the following to your `configuration.yaml`:

```yaml
zha:
  custom_quirks_path: /config/custom_zha_quirks
```

### 2. Create the quirks directory

Connect to your Home Assistant instance via SSH or terminal and run:

```bash
mkdir -p /config/custom_zha_quirks/ts0014
```

### 3. Clone this repo and copy the files

```bash
cd /tmp
git clone https://github.com/ksaitor/TS0014-lerlink-4g-fix.git
cp TS0014-lerlink-4g-fix/ts0014.py /config/custom_zha_quirks/ts0014/
cp TS0014-lerlink-4g-fix/__init__.py /config/custom_zha_quirks/ts0014/
rm -rf /tmp/TS0014-lerlink-4g-fix
```

**Alternative (no git):** Download the two files directly:

```bash
cd /config/custom_zha_quirks/ts0014
wget https://raw.githubusercontent.com/ksaitor/TS0014-lerlink-4g-fix/master/ts0014.py
wget https://raw.githubusercontent.com/ksaitor/TS0014-lerlink-4g-fix/master/__init__.py
```

### 4. Restart Home Assistant

```bash
ha core restart
```

Or restart from the UI: **Settings > System > Restart**.

### 5. Re-pair the device

If the quirk doesn't apply automatically after restart:

1. Go to **Settings > Devices & Services > ZHA**
2. Remove the TS0014 device
3. Re-add it through ZHA's "Add Device" flow

### 6. Verify

Check the ZHA device info page for your TS0014. You should see:

- `quirk_applied: True`
- `quirk_class` containing `TS0014_Switch_4G`
- 4 separate switch entities, one per gang

You can also check the logs:

```bash
ha core logs | grep -iE "quirk|TS0014"
```

## File Structure

```
/config/custom_zha_quirks/
  ts0014/
    __init__.py       # Required empty file (makes it a Python package)
    ts0014.py         # ZHA quirk definitions for TS0014 variants
```

## Supported Variants

This quirk file includes several signature variants to cover different TS0014 hardware revisions:

| Class | Description |
|---|---|
| `TS0014_Switch_4G` | Simple variant (e.g. `_TZ3000_sovlwiix`) -- no EP 242, no Tuya manufacturer clusters |
| `TS0014_Switch_4G_GPP` | Variant with Green Power Proxy + external switch type clusters |
| `TS0014_Switch_4G_Metering` | Variant with metering and electrical measurement clusters |
| `TS0014_Switch_4G_GPP_Var2` | Variant 2 with Green Power Proxy + Tuya clusters on all endpoints |

ZHA tries each class until one matches your device's actual endpoint/cluster layout.

## License

MIT
