pahole
------

pahole helps examin paddings in executables. Some tricks to compile and use it.

Install
=======

The `pahole` included in Ubuntu (I'm stuck with 14.04 at the moment) does not support C++11.
So I choose to compile the latest one.

The official git is at https://git.kernel.org/pub/scm/devel/pahole/pahole.git .
However, compiling it on Ubuntu needs some efforts. I forked it to applied some
patch and notes: https://github.com/beyondwdq/pahole . Pls refer to my latest commits
on the forked repository, and follow the README to install it.

Pls note if you don't want to install it, the executable `pahole` is generated in the current
directory. You can directly run it with `./pahole`

Usage
=====

You'll need to use the struct at least once to make the compiler to include the struct
in the binary.

    struct A
    {
        int i;
    };

    int main(int argc, char *argv[])
    {
        A a;
        return a.i;
    }

Compile and then run `pahole ./a.out`.
