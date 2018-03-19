# PCF Fonts #
PCF fonts are bitmap fonts that can be used in Unix-like environments running
X11.  Often they are distributed in "source" form as `.bdf` files which are
"compiled" to `.pcf` files by the utility `bdftopcf` or similar.

## System Check ##
Execute `xset q` and look for the lines about "Font Path" which should help
pinpoint where to install bitmap PCF fonts.  Typically this will be something
like `/usr/share/fonts/X11/misc` but the important part is the folder "misc".

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

## IBM PC Fonts ##
[source](http://int10h.org/oldschool-pc-fonts/),
[BDF source](https://farsil.github.io/ibmfonts/)

### Ubuntu ###
Check the configuration script for instances of `bdf2pcf` and change them to
`bdftopcf` if needed.  (Make sure the appropriate executable is installed.)

Configure and install the font:
```
./configure --installdir=/usr/share/fonts/X11/misc/
make
sudo make install
```

In the directory `/usr/share/fonts/X11/misc` run `mkfontdir` and maybe
`mkfontscale` and maybe `xset fp rehash` and the fonts should appear in
`xfontsel` menus.  (Look for foundry "ibm".)
