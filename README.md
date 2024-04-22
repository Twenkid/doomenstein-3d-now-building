# Wolfenstein/DOOM style software renderers

![project screenshot](screen/image.png)

* `src/main_doom.c`: a DOOM-style software renderer
* `src/main_wolf.c`: a Wolfenstein 3D-style software renderer

### Building & Running

`$ make doom|wolf|all`, binaries are `bin/doom` and `bin/wolf` respectively

**Todor/Twenkid:** The original repo didn't build out of the box (WSL2 and native Linux Ubuntu 22.04).

This is how I fixed it in order to run on both platforms (_SOFTWARE is not required for the native Linux one if you have a GPU)

```gcc -o bin/src/main_doom.o -MMD -c -std=c2x -O2 -g -fbracket-depth=1024 -fmacro-backtrace-limit=0 -Wall -Wextra -Wpedantic -Wfloat-equal -Wstrict-aliasing -Wswitch-default -Wformat=2 -Wno-newline-eof -Wno-unused-parameter -Wno-strict-prototypes -Wno-fixed-enum-extension -Wno-int-to-void-pointer-cast -Wno-gnu-statement-expression -Wno-gnu-compound-literal-initializer -Wno-gnu-zero-variadic-macro-arguments -Wno-gnu-empty-struct -Wno-gnu-auto-type -Wno-gnu-empty-initializer -Wno-gnu-pointer-arith -Wno-c99-extensions -Wno-c11-extensions -iquotesrc src/main_doom.c
gcc: error: unrecognized command-line option ‘-fbracket-depth=1024’
gcc: error: unrecognized command-line option ‘-fmacro-backtrace-limit=0’; did you mean ‘-ftemplate-backtrace-limit=’?
make: *** [Makefile:77: bin/src/main_doom.o] Error 1```

1. Comment these lines in the Makefile.txt

Then you may get SDL.h not found:

```
gcc -o bin/src/main_doom.o -MMD -c -std=c2x -O2 -g -Wall -Wextra -Wpedantic -Wfloat-equal -Wstrict-aliasing -Wswitch-default -Wformat=2 -Wno-newline-eof -Wno-unused-parameter -Wno-strict-prototypes -Wno-fixed-enum-extension -Wno-int-to-void-pointer-cast -Wno-gnu-statement-expression -Wno-gnu-compound-literal-initializer -Wno-gnu-zero-variadic-macro-arguments -Wno-gnu-empty-struct -Wno-gnu-auto-type -Wno-gnu-empty-initializer -Wno-gnu-pointer-arith -Wno-c99-extensions -Wno-c11-extensions -iquotesrc src/main_doom.c
src/main_doom.c:5:10: fatal error: SDL.h: No such file or directory
    5 | #include <SDL.h>
      |          ^~~~~~~
compilation terminated.
```

The project is using SDL2, the paths may be not properly set.
My solution was to change the paths in the code files to explicit ones.
One may have to install SDL2
```
sudo apt update
sudo apt-get install libsdl2-dev
or sudo apt install libsdl2-dev...
```

Change the includes in main_doom.c and main_wolf.c

to

**"/usr/include/SDL2/SDL.h"**

```
//#include <SDL.h>
#include </usr/include/SDL2/SDL.h>
```

instead of just <SDL.h>

make wolf
make doom
make all

Run:
```
./bin/doom
./bin/wolf
```
Use the Arrow keys (not WASD).

The Wolf demo doesn't have _HARDWARE, but one fix there is slowing down the movement and rotation.

```
        const f32
            rotspeed = 1.0f * 0.016f,  //3.0* -- too fast
            movespeed = 1.0f * 0.016f; //3.0*
```
That should be a var, not a const for future work - for running etc.

Also intially you should go back in Wolf - if you go out of the map the program crashes.





