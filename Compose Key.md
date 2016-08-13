# Compose Key #
The compose key allows for the entering of additional characters beyond what the
basic keyboard allows.  It is pressed and released, then followed by two or more
normal key presses to enter an alternate character.

## Basic Diacritics ##
Apply to upper-case and lower-case vowels, and other consonants as appropriate.

| Key Combo  | Result   |
| ---------- | -------- |
| `' a`      | á        |
| `" a`      | ä        |
| ``\` a``   | à        |
| `~ a`      | ã        |
| `^ a`      | â        |
| `c a`      | ǎ        |
| `o a`      | å        |
| `_ a`      | ā        |
| `; a`      | ą        |
| `b a`      | ă        |
| `? a`      | ả        |

## Cygwin/X (xwin) ##
Verified that it is _not_ possible to change the settings with the ~/.Xmodmap
file.  See also question 5.1.3 of the Cygwin/X FAQ:
[http://x.cygwin.com/docs/faq/cygwin-x-faq.html](http://x.cygwin.com/docs/faq/cygwin-x-faq.html)

Strangely, the compose key will _not_ work if the window has been Alt-Tabbed
into from a native Windows application and the mouse cursor has not yet passed
over a Cygwin X window.  If the compose key doesn't work, click on the window in
question and try again.

### View the Existing Keymap ###
Issue the following commands, then view the resulting PDF file:
```
xkbprint :1 keymap.ps
ps2pdf keymap.ps
```

### Show the Keymap Settings ###
The command `set xkbmap -query` will show the following settings by default:
```
    rules:      base
    model:      pc105
    layout:     us
```

### Enable the Compose Key ###
Use the following command to enable the compose key on the Shift + Right Alt
keys.  (Note that the order is non-transitive; Shift must be pressed first.)
```
setxkbmap -option lv3:ralt_switch_multikey
```
