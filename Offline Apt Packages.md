# Offline Apt Packages #
Use `apt-offline` available from
[https://apt-offline.alioth.debian.org/](https://apt-offline.alioth.debian.org/)

## Workflow ##
On the isolated system:
```
sudo apt-offline set --update isolated.sig
```
**OR**
```
sudo apt-offline set --install-packages php5 --update isolated.sig
```
**OR**
```
sudo apt-offline set --upgrade isolated.sig
```
(which lists all files that have an updated version)

On the connected system:
```
apt-offline get isolated.sig --bundle isolated.zip
```

Back on the isolated system:
```
sudo apt-offline install isolated.zip
```

Finally, use usual package managers to actually install packages (since
apt-offline just gets them into the local cache).

## Issues ##
On Windows/Cygwin with apt-offline-0.9.9:
In `/usr/lib/python2.7/site-packages/apt_offline_core/AptOfflineLib.py` line 69,
change string `"md5"` to `"md5sum"`

In the same file, on line 84 change the right-hand side to `hashlib.md5()`

## Package List from Aptitude ##
Mark packages as desired in aptitude.  Quit.
```
aptitude search "!(~akeep | ~M)"
```
Selects packages that will not be kept (i.e. installs or upgrades) and packages
that are not automatic (i.e. auto-resolved dependencies).

TODO may need some work
