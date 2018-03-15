# PCF Fonts #
PCF fonts are bitmap fonts that can be used in Unix-like environments running
X11.  Often they are distributed in "source" form as `.bdf` files which are
"compiled" to `.pcf` files by the utility `bdftopcf` or similar.

## Terminus ##
[source](http://terminus-font.sourceforge.net/)

Patch the font with `td1` and `br1` variants:
```
patch -p1 -i alt/td1.diff
patch -p1 -i alt/br1.diff
```

### Cygwin/X ###
Create the directory `/usr/share/fonts/terminus` with permission settings `755`
being sure to match the user/group ownership with sibling directories.

Configure and install the font:
```
./configure --x11dir=/usr/share/fonts/terminus/
make pcf
sudo make install-pcf
```

In the directory `/usr/share/fonts/terminus` run `mkfontdir` and maybe
`mkfontscale` (or try `make fontdir` if available).

Confirm that terminus is listed in the font list and references the proper
files:
```
fc-list | grep -i term
```

### Ubuntu ###
(incomplete)

Configure and install the font:
```
./configure --x11dir=/usr/share/fonts/X11/misc/
make pcf
sudo make install-pcf
```
