In `.bash_profile`
```
PATH=$HOME/.local/bin:$PATH
LD_LIBRARY_PATH=$HOME/.local/lib:$LD_LIBRARY_PATH

export PATH
export LD_LIBRARY_PATH
```

`. .~/bash_profile`

libevent

`./configure --prefix=$HOME/.local`

tmux:

`CFLAGS="-I$HOME/.local/include" LDFLAGS="-L$HOME/.local/lib" ./configure  --prefix=$HOME/.local`
