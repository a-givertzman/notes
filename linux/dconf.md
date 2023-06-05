# Dconf

Dconf is the low-level configuration system used by the GNOME desktop environment. It is basically a database, where the various configuration are stored as keys together with their values. The keys in the database can be inspected, changed, or dumped with the dconf utility or by using the dconf-editor graphical tool.

- Listing the keys in a database

```bash
dconf list /
dconf list /org/
dconf list /org/app-name/
dconf list /org/app-name/section-name/
```

- Retrieving the value of a key

```bash
dconf read /org/gnome/desktop/background/picture-uri
```

- Changing the value of a key

```bash
dconf write /org/onboard/window/olways-on-top true
dconf write /org/gnome/desktop/background/picture-uri "'file:///home/pics/wallpapers/3.jpg'"
```
