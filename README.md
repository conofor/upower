# UPower

> [!NOTE]
> **About this tree** — the default branch, `fix/bluetooth-hid-battery`,
> carries one fix on top of the upstream 1.90.9 release: upowerd reads
> peripheral battery values (Bluetooth HID devices such as the Apple
> Magic Keyboard) from a worker thread instead of blocking the daemon's
> main loop on sysfs, so their charge actually shows up on the desktop.
> See the [fix commit][fix] and the [project overview][overview]
> (driver + bluez plugin + this fix).  The `master` branch mirrors
> upstream upower unchanged.
>
> [fix]: https://github.com/conofor/upower/commit/40c48f93238ef48f5b7ca457e673698f9648d167
> [overview]: https://github.com/conofor/hid-apple-bt-battery

Requirements:

```text
  glib-2.0             >= 2.66.0
  gio-2.0              >= 2.16.1
  gudev-1.0            >= 235    (Linux)
  libimobiledevice-1.0 >= 0.9.7  (optional)
  polkit-gobject-1     >= 124
```

UPower is an abstraction for enumerating power devices,
listening to device events and querying history and statistics.
Any application or service on the system can access the
org.freedesktop.UPower service via the system message bus.

## Debugging

When doing bug reports, the following information can be useful:

- `grep . /sys/class/power_supply/*/*`  
  This includes the current kernel view of all power supplies in the
  system. It is always a good idea to include this information.
- `udevadm info -e`  
  This shows the hardware configuration and is relevant when e.g. the
  type of an external device is misdetected.
- `upower -d`  
  Shows upower's view of the state
- `upower --monitor-detail`  
  Dumps device information every time that a change happens. This helps
  with debugging dynamic issues.
- `udevadm monitor`  
  Dumps the udev/kernel reported hardware changes (and addition/removal).
  This is helpful when debugging dynamic issues, in particular if it is
  not clear whether the issue is coming from the kernel or upower.

In addition, it can also be useful to run upower in debug mode and post the
logs. There are two ways of doing so:

- Run upower daemon manually, you can do so using:
  `sudo /usr/libexec/upowerd -rd`
- Modify the systemd service and restart. This is best done by:
  1. `sudo systemctl edit upower.service`
  2. Adding the two lines:

      ```text
      [Service]
      Environment=G_MESSAGES_DEBUG=all
      ```

  3. `sudo systemctl restart upower.service`
  4. Grab logs using `journalctl -u upower.service` or similar
