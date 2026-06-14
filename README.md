# udev-rules

Persönliche [udev](https://wiki.archlinux.org/title/Udev)-Regeln für stabile Gerätenamen und Zugriffsrechte auf serielle Schnittstellen, HID-Geräte und Amateurfunk-Equipment.

## Enthaltene Regeln

| Datei | Zweck |
|-------|-------|
| `98-serial.rules` | Symlink für eine USB-Seriell-Schnittstelle |
| `99-hamradio.rules` | Symlinks für Funkgeräte und Peripherie |
| `99-hidraw-permissions.rules` | Zugriffsrechte für HID-Rohgeräte |

### 98-serial.rules

Erzeugt den Symlink `/dev/ttyUSB_serial1` für ein USB-Seriell-Gerät (Vendor `1d6b`, Product `0002`, Serial `0000:02:00.0`).

### 99-hamradio.rules

Stabile Gerätenamen unter `/dev/`:

| Symlink | Gerät |
|---------|-------|
| `hamIC-7300` | ICOM IC-7300 |
| `hamIC-9700_A` | ICOM IC-9700 (VFO A) |
| `hamIC-9700_B` | ICOM IC-9700 (VFO B) |
| `hamSCSdragon` | SCS Dragon (FTDI, `0403:d013`) |

Die Regeln matchen über USB-Vendor-ID und Seriennummer, damit sich die Geräte auch nach Neustart oder Umstecken wieder unter demselben Namen finden lassen.

### 99-hidraw-permissions.rules

Setzt `MODE="0666"` und `GROUP="plugdev"` für alle `hidraw`-Geräte, damit Anwendungen ohne Root-Rechte darauf zugreifen können.

## Installation

```bash
git clone https://github.com/oe3gwu/udev-rules.git
cd udev-rules
sudo cp *.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Optional: Benutzer zur Gruppe `plugdev` hinzufügen (für HID-Zugriff):

```bash
sudo usermod -aG plugdev $USER
```

Danach ab- und wieder anmelden.

## Anpassen

Die Seriennummern in `99-hamradio.rules` sind gerätespezifisch. Eigene Werte ermitteln:

```bash
udevadm info -a -n /dev/ttyUSB0 | grep -E 'serial|idVendor|idProduct'
```

Regeldatei bearbeiten, nach `/etc/udev/rules.d/` kopieren und udev neu laden (siehe oben).

## Hinweise

- **Reihenfolge:** Dateinamen mit niedrigerer Nummer (z. B. `98-`) werden vor höheren (`99-`) ausgewertet.
- **Sicherheit:** `MODE="0666"` auf `hidraw` erlaubt jedem lokalen Benutzer Lese- und Schreibzugriff. Nur verwenden, wenn das für die jeweilige Anwendung nötig ist.
- **Testen:** Nach dem Anstecken eines Geräts prüfen, ob der Symlink existiert:

  ```bash
  ls -l /dev/ham* /dev/ttyUSB_serial1
  ```

## Lizenz

Keine explizite Lizenz angegeben — bei Weiterverwendung bitte Rücksprache mit dem Repository-Inhaber halten.
