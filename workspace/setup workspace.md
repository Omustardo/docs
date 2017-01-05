Instructions for setting up my work environment. I recommend doing the
installations in order, as there are some dependencies. **TODO: Add links and
better markdown. See if git bash comes with gcc (which version?), and if so try
to do this without mingw.**

General tools / workflow:

Write code in IntelliJ IDEA

Upload code to Github using the Github desktop client

Command line needs use Git Bash - a bash shell for Windows.

C compiler is mingw64's gcc

**Github Desktop & Git Bash**
-----------------------------

<https://desktop.github.com/>

Github Desktop is not required, but makes working with Github more pleasant. It
also installs Git Bash, which is very handy. Effectively emulates bash on
Windows, and gives the ability to right-click open a terminal from any folder.
TODO: [@omustardo] The right click option didn’t seem to show up in the default
installation? “When installing Git for Windows the context menu options are not
'on' by default. You will have to select them during the install.” Alternate:
get it with Git SCM here https://git-for-windows.github.io/

Follow basic installation, and log in with your Github account. Run the program
and go into settings. Change the default shell to Git Bash. Change the default
clone path to: `C:\workspace\go\src\github.com\omustardo` with your username
instead of `omustardo`

**MinGW-w64**
-------------

<https://sourceforge.net/projects/mingw-w64>

MinGW has a bunch of the utilities you expect on linux, like `ls`, `cd`, `grep`,
and most importantly for us, `gcc`.

Run the installer. For architecture, choose `x86_64`. You can leave it as the
default, but I recommend changing the installation path to `C:\mingw` to avoid
the long default path name.

TODO: [@omustardo] is this really needed? Does git bash install these by
default?

**Install Golang**
------------------

TODO: [@omustardo] : Link/basic install instructions

Set `GOPATH` and `GOROOT`. `GOROOT` is where compiler/tools from the Go
installation should go. `GOPATH` is for all other code, including what you
write. I use `GOPATH=C:\workspace\go` and `GOROOT=C:\go`  
Also set `GOBIN=C:\workspace\go\bin` and add the same file path to your
environmental variable’s PATH.

If you're following this document in order, MinGW is set up at this point. Let's
set it up so you can use Go libraries that use C code. This requires making sure
the Go tool can find mingw's gcc. You can see that Go's `C` compiler is `gcc` by
default, by running `go env`. This is correct, but it won't work as is. You can
try it yourself by running `gcc`. It probably says something about program not
found, unless you already have another `gcc` installed on your system.

To make `gcc` work, you need to make sure `gcc` is in `PATH`. When I did this,
the default placement is: `C:\Program
Files\mingw-w64\x86_64-6.2.0-posix-seh-rt_v5-rev1\mingw64\bin` but if you
followed my advice in the MinGW installation, it's: `C:\mingw\mingw64\bin`

TODO: Install useful Go tools: goimport, govet, ... ?

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
go get golang.org/x/tools/cmd/goimports
go get github.com/golang/lint/golint
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Make sure everything works**
------------------------------

`go get -u github.com/go-gl/gl/v2.1/gl`

`go get -u github.com/go-gl/glfw/v3.2/glfw`

`go get -u github.com/go-gl/examples/gl21-cube`

`go run C:/workspace/go/src/github.com/go-gl/examples/gl21-cube/cube.go`

A spinning cube should appear. This demo depends on C code, so if it works then
you did everything right.

**IntelliJ**
------------

<https://www.jetbrains.com/idea/#chooseYourEdition>

I use the community edition (as of 12/11/2016, I use version 2016.2.5). Install
with default settings. When first run, it will ask if you want to import
previous settings. If you have them, they should be in a jar file. Unfortunately
it only allows specifying an entire directory, so tell it you don't have a
config and continue. Use default settings - they'll be overriden soon enough. In
the "Welcome to IntelliJ IDEA" window click on config-\>import settings, and
then choose the jar file.

TODO: More explanation on how to create an overarching project in
go/src/github.com/omustardo and then another for each project directory. I’m not
sure if this is really best.. it makes it tough to work on multiple projects at
once.

If you have it, put .editorconfig in project root dir. This overrides the
default formatting. TODO: Upload and link example file. Example files can be
found in most of my repositories.

TODO: Get goimport, golint, and gofmt to run on save. This requires installing
the File Watchers plugin.
https://rootpd.com/2016/02/04/setting-up-intellij-idea-for-your-first-golang-project/

TODO: Fix unresolved references. File -\> Project Structure -\> Project Settings
-\> Modules. Remove current Content Root and manually Add Content Root. Set it
to your Go source dir. For me, it’s C:\\workspace\\Go\\src

**Misc**
--------

### Protoc

Install protoc

Install protoc for golang

Make sure to add the location of the protoc binary to your PATH.

Normally you compile .proto files with `protoc --go_out=. <target>` but this
gets annoying really quickly. An option is to add a small bash script to do it.
On windows you could make a file called `gopro.bat` and put `protoc --go_out=.
%1` into it. Then you can run `gopro.bat foo.proto` as long as the batch file is
in PATH.

It may be possible to avoid all of this and have automatic proto compilation in
Intellij.

### Notepad++

### Texts - Markdown helper

Consider removing this. Not needed anymore now that I use intellij.

### CMD

If you use the windows command prompt, you can make it start in a more useful
directory:

Create a shortcut. Set destination as `cmd`, and target as:
`C:\Windows\System32\cmd.exe /K "cd
C:\Projects\Go\src\github.com\username\project"`

**go tool pprof**
-----------------

If you run `go tool pprof $EXE $INFILE` you can use `top10` and other basic
commands without issue.

In order to use the graph visualization of Go’s profiler (pprof), you need to
install graphviz. I got the .msi
[here](<http://www.graphviz.org/Download_windows.php>). Once it was installed,
add its bin (C:\\Program Files (x86)\\Graphviz2.38\\bin) directory to PATH.
Unfortunately there is a problem with its implementation in combination with
mingw bash. Normally you can run the `web` or `png` commands (among others) and
the appropriate graphviz program will produce results. Since this is windows,
the executable names all end with .exe, which isn’t handled in mingw. I ended up
having to run pprof through a standard windows command prompt so it got the
executable names right.
