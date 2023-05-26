# Installing .so file on Debian

- copy the .so file to /usr/local/lib Directory

```bash
sudo cp /lib.so /usr/lib
```

- Update The Shared Library Cache Using ldconfig Command

```bash
sudo ldconfig
```
