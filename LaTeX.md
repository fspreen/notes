# LaTeX #

## User-local Packages ##
A LaTeX style which includes additional files such as images should have them
placed in the same directory as the `.sty` file and referred to without path
information.  For example:
```tex
\includegraphics[height=3in]{image_file_name.png}
```

### Cygwin ###
To find the folder, use the command
```
kpsewhich -var-value=TEXMFHOME
```

In Cygwin, this is located at `~/.local/share/texmf/` by default.  A LaTeX
style would be placed in `~/.local/share/texmf/latex/STYLE/STYLE.sty`

### MiKTeX ###
Folders are available from "Start > All Programs > MiKTeX > Maintenance >
Settings" and using the "Roots" tab.
