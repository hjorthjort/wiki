This is quick and dirty. It's by no means minimal, just the fastest way to get a new machine feeling like my regular one.

```
cd ~
git clone git@github.com:hjorthjort/dotfiles.git .dotfiles
cd .dotfiles
./linker.sh

cd ~
git clone --recursive git@github.com:hjorthjort/zprezto.git .zprezto // Check out 
cd .zprezto
# Run the script described in the Readme.
```

That's enough to keep you sane.

Remote machines
===============

`zprezto` has a remote branch, use that one.

Share clipboard across local and remote
---------------------------------------
This one is a lifesaver.

You'll want X11 forwarding, at least for the clipboard.


https://unix.stackexchange.com/questions/12755/how-to-forward-x-over-ssh-to-run-graphics-applications-remotely

First, install `xclip`

Then, in `sudo vim /etc/ssh/sshd_config` set some variables

```
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost no
```

Then add these to the correct host in `~/.ssh/config`. They correspond to the `-X` and `-Y` flag, respectively.
Only use the `Trusted` option for really trusted machines, since they allow the remote to access any X resources (such as the clipboard).

```
    ForwardX11 yes
    ForwardX11Trusted yes
```
