# Virtual Environments

Virtual Python environments allow for Python interpreters and packages to be
contained in a specific folder, often for development purposes.  Environments
can be activated and deactivated at will.

## Creating a Virtual Environment
Virtual environments are created with the `virtualenv` command.  This is
provided by the operating system but is not part of the basic Python packaging.

The basic command is as follows:
```sh
virtualenv -p python3 foldername
```

This creates a new virtual environment "foldername" with the Python 3
interpreter.  (The `-p` flag can take a path to any Python interpreter.)

### Alternate Creation
Python includes its own virtual environment utility beginning with 3.3 called
`pyenv` but beginning with 3.6 this is deprecated in favor of the `venv` module.
Virtual environments are created similarly:
```sh
python -m venv foldername
```

The `venv` module may have some shortcomings, notably that Python 2 is not
available.  Nesting environments is also problematic and the two methods should
not be mixed.  (A nesting scenario is also probable when using "tox" which
creates a virtual environment for testing.)

### Activating and Deactivating
The virtual environment can be activated for the current shell by invoking:
```sh
. venv/bin/activate
```
The dot can be replaced with `source` but the script is _not_ invoked as a
normal executable.

The script will by default modify the Bash prompt string with the name of the
virtual environment.  It will also add a new shell function `deactivate` which
disables the virtual environment again.

While the virtual environment is activated, the `$PATH` variable is modified and
the variable `$VIRTUAL_ENV` is set to the name of the virtual environment.

## Caveat
The virtual environment folder **should not be moved or renamed**.  The contents
are cross-referenced with absolute file paths.

## What's Inside
The virtual environment contains a symbolic link (or straight copy) of the
relevant Python interpreter.  It also contains the `pip` utility for package
management, `setuptools`, `wheel`, and basic Python libraries and supporting
files.

If the local `pip` utility is used to manage packages, then any packages it
installs will also be installed to the virtual environment.

Finally, there is also an activation/deactivation shell script in `bin/` which
allows the virtual environment to be activated/deactivated in the current shell.
(On Windows, this may be a `.bat` file.)

## Links
* [Python venv Module](https://docs.python.org/3/library/venv.html)
* [virtualenv utility](https://virtualenv.pypa.io/en/latest/)
