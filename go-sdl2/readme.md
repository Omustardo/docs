Installation instructions for <https://github.com/veandco/go-sdl2>
------------------------------------------------------------------

In short, you need to install mingw64 and make sure Go references the mingw64
gcc rather than some other 32 bit one. You also need to unpack the SDL2 files
and put them in the proper locations so gcc can find them.

Cygwin is an alternative, but according to some old SDL docs, it is expected to
stop working in the future.

1.  Install mingw-64  
    Make sure to set Architecture to x86\_64 but otherwise leave all of the
    settings the same. You can change the install path if you want. I set mine
    to C:\\mingw  
    Make sure to add the mingw bin to your PATH. For me, it's
    C:\\mingw\\mingw64\\bin

2.  Setting Go's C compiler: On command line, you can run "go env" to see the Go
    specific environmental variables. We're interested in GCCGO. Go into your
    Windows environmental variables and set GCCGO to point to gcc. For me it's:
    C:\\mingw\\mingw64\\bin\\gcc

3.  Download SDL2 from <https://www.libsdl.org/download-2.0.php> Make sure to
    choose the tar.gz for MinGW. Unpack it. It has a bunch of junk in it that
    you don't need to worry about. The only folder that matters is
    `x86_64-w64-mingw32`. Open that folder along with the `mingw/mingw64`
    folder. Copy everything from `x86_64-w64-mingw32` into `mingw64`. If done
    correctly, you should have some messages about folders with duplicate names.
    Merge them all. SDL2 is now installed.

4.  (optional, but highly recommended) Compile a test program. Rather than
    attempting to debug "go get", make sure SDL2 is working by compiling a
    simple program:

    ```
    #include "SDL2/SDL.h" 
    #include <stdio.h>
    int main( int argc, char* args[] ) { 
        SDL_Window *window; 
        SDL_Init(SDL_INIT_VIDEO); 
        window = SDL_CreateWindow("SDL2 Window", 100, 100, 640, 480, 0); 
        if(window==NULL) {
            printf("Could not create window: %s\n", SDL_GetError());
            return 1; 
        } 
        SDL_Delay(3000); 
        SDL_DestroyWindow(window); 
        SDL_Quit(); 
        return 0;
    }
    ```

    The code above is from:
    <https://w3.cs.jmu.edu/bernstdh/Web/common/help/cpp_mingw-sdl-setup.php>  
    Save it in a file called test.c

    Now open a terminal and go to wherever you saved the file. Make sure gcc is
    the one you installed with mingw-64. If you're on a standard windows
    terminal, run "`where gcc.exe`". If you've installed something like Git Bash
    you can use linux-like commands, so: "`which gcc`". Either way, you should
    get the correct path. For me, it says `C:\MinGW\mingw64\bin\gcc`  
    Compile the program with: `gcc.exe -o test.exe test.c -lmingw32 -lSDL2main
    -lSDL2`  
    Now if it works and you can run the executable, you're all set. Continue to
    the next step.

    If you're like me, you got an error about SDL2/SDL.h not existing. It seems
    the default paths for SDL2's include folder don't match mingw's. I found the
    correct location by running the compilation command with -v for verbose
    logging: `gcc.exe -v -o test.exe test.c -lmingw32 -lSDL2main -lSDL2`  
    It shows where gcc looks for includes, which is where we need to put the
    SDL2 files. So, go back to `SDL2-2.0.4\x86_64-w64-mingw32` and
    `C:\mingw\mingw64` folders from step 3.  
    Copy the SDL2 folder from `SDL2-2.0.4\x86_64-w64-mingw32\include` and paste
    it in `C:\mingw\mingw64\lib\gcc\x86_64-w64-mingw32\6.2.0\include`  
    The program should now compile and you can run test.exe to see an SDL
    window. Congrats.
	
1.  Get Golang bindings for SDL.  
    `go get -v github.com/veandco/go-sdl2/sdl`

2.  Test Go SDL cd to the code you got in the previous step and try running
    samples.  
    `cd "github.com\veandco\go-sdl2\examples"`  
    `go run render\render.go`

3.  (optional) Install SDL mixer, image, and ttf

    Installation process is exactly the same. Get the tar.gz files:  
    http://www.libsdl.org/projects/SDL\_mixer/  
    http://www.libsdl.org/projects/SDL\_image/  
    http://www.libsdl.org/projects/SDL\_ttf/

    Unpack them into `C:\mingw\mingw64` and copy files from the include folder
    into `C:\mingw\mingw64\lib\gcc\x86_64-w64-mingw32\6.2.0\include`

    Then:  
    `go get -v github.com/veandco/go-sdl2/sdl_mixer`  
    `go get -v github.com/veandco/go-sdl2/sdl_image`  
    `go get -v github.com/veandco/go-sdl2/sdl_ttf`
