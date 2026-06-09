# bledfu

No frills, no bloat, no telemetry getting sent anywhere.

See https://thandal.github.io/bledfu/

A single-page [Web Bluetooth](https://webbluetoothcg.github.io/web-bluetooth/) tool for updating
Nordic device firmware over Bluetooth LE using the [secure DFU](https://infocenter.nordicsemi.com/topic/com.nordic.infocenter.sdk5.v12.0.0/lib_dfu_transport_ble.html)
protocol. The whole app is `index.html` — no build step, no dependencies to install. Open it in a
Web Bluetooth capable browser (desktop Chrome/Edge) over `https://` or `localhost`.

## Usage

1. **Add a target** once with **+ Add target** (this is the only Bluetooth chooser popup).
   The browser remembers it, so on later visits it appears in the list ready to click.
2. **Pick a firmware `.zip`** (Nordic DFU package) by clicking the drop zone or dragging a file in.
3. **Click the target, then Upload firmware.** Done.

Re-uploading the same file to the same target is a single click. Switching target or file is
just clicking another card / picking another file — no reload needed.

### Buttonless devices

A device running normal application firmware (with the buttonless DFU service) reboots into its
bootloader before it can be flashed. The bootloader is a *separate* BLE device:

- The **first** time, you'll be asked to pick it from a chooser once.
- After that it's a remembered target, so subsequent uploads reconnect to it silently.

## Persistent pairing

One-click reconnects rely on Chrome's persistent Web Bluetooth permissions
(`navigator.bluetooth.getDevices()`). If targets aren't being remembered across reloads, enable:

```
chrome://flags/#enable-web-bluetooth-new-permissions-backend
```

Without it, targets simply aren't remembered across reloads — re-add them with **+ Add target**.

## Running locally

Serve the directory over `localhost` (a secure context Web Bluetooth accepts):

```
python3 -m http.server 8000
# then open http://localhost:8000/
```

## Firmware packages

Packages are standard Nordic DFU `.zip` files (a `manifest.json` plus `.dat`/`.bin` images),
built with `nrfutil`:

```
nrfutil pkg generate --application <your_app.hex> --key-file private.key dfu_app.zip
```

A package may contain a base image (softdevice / bootloader / softdevice+bootloader) and/or an
application image; both are flashed in order.

## License

MIT — see [LICENSE](LICENSE).
