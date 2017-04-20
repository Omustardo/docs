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
also installs Git Bash, which is very handy. 

Follow basic installation, and log in with your Github account. Run the program
and go into settings. Change the default shell to Git Bash. Change the default
clone path to: `C:\workspace\go\src\github.com\omustardo` with your username
instead of `omustardo`

Also install https://git-for-windows.github.io/ to get bash, and the ability to
right-click open a terminal from any folder.

**MinGW-w64**
-------------

<https://sourceforge.net/projects/mingw-w64>

MinGW has a bunch of the utilities you expect on linux, like `ls`, `cd`, `grep`,
and most importantly for us, `gcc`.

Run the installer. For architecture, choose `x86_64`. You can leave it as the
default, but I recommend changing the installation path to `C:\mingw` to avoid
the long default path name.

Add mingw binaries to `PATH`. When I did this the default placement was: `C:\Program
Files\mingw-w64\x86_64-6.2.0-posix-seh-rt_v5-rev1\mingw64\bin` but if you
followed my advice in the MinGW installation, it's: `C:\mingw\mingw64\bin`

**Install Golang**
------------------

https://golang.org/doc/install

Install using the MSI installer for Windows.

Set `GOPATH` and `GOROOT`. `GOROOT` is where compiler/tools from the Go
installation should go. `GOPATH` is for all other code, including what you
write. I use `GOPATH=C:\workspace\go` and `GOROOT=C:\go`  
Also set `GOBIN=C:\workspace\go\bin` and add the same file path to your
environmental variable’s PATH.

You can run `go env` to see what Go environmental variables are set to. This
is very handy for debugging any issues you run into.

Install useful Go tools:
```
go get golang.org/x/tools/cmd/goimports
go get github.com/golang/lint/golint
```

**Make sure everything works**
------------------------------

`go get -u github.com/go-gl/gl/v2.1/gl`

`go get -u github.com/go-gl/glfw/v3.2/glfw`

`go get -u github.com/go-gl/examples/gl21-cube`

`go run C:/workspace/go/src/github.com/go-gl/examples/gl21-cube/cube.go`

A spinning cube should appear. This demo depends on C code, so if it works then
you did everything right.

**Gogland**
-----------

<https://www.jetbrains.com/go/>

Gogland is an alternate to IntelliJ, and recommended if you only use Golang.

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

Install protoc: https://github.com/google/protobuf/releases/tag/v3.0.0

Releases are at the bottom of the page. I downloaded the win32 version and extracted just the executable.
The result was: `C:\Program Files (x86)\protoc\protoc.exe`

Make sure to add the location of the protoc binary to your PATH.

Install the golang protoc plugin: `go get -u github.com/golang/protobuf/protoc-gen-go`

You can compile .proto files with `protoc --go_out=. <target>` which puts the resulting
 .pb.go file in the same directory as you're in.

Note that if you have multiple protos which depend on each other, you need to 
use protoc from `$GOROOT`. Alternatively, set `go_package` inside the protos
to where others will be importing it from e.g. `github.com/myorg/myrepo/mypackage`.

### gRPC

http://www.grpc.io/docs/quickstart/go.html

gRPC requires having already installed protoc, so follow the steps above if you haven't yet.
Then install gRPC:

`go get google.golang.org/grpc`

Make sure it works by building the example in `$GOPATH/src/google.golang.org/grpc/examples`

```
cd $GOPATH/src/google.golang.org/grpc/examples/helloworld
rm helloworld/helloworld.pb.go
protoc -I helloworld/ helloworld/helloworld.proto --go_out=plugins=grpc:helloworld
```
Alternatively, if you start in the same directory as the proto file:
```
protoc helloworld.proto --go_out=plugins=grpc:.
```
In two separate terminals, run:

`go run greeter_server/main.go`

`go run greeter_client/main.go`

If things go smoothly, you will see the "Greeting: Hello world" in the client side output.

Note the strange syntax in `--go_out=plugins=grpc:helloworld`. This is because `protoc` is a general purpose
proto compiler, and it needs to call a separate program (they call it a plugin) in order to compile protos.
In this case, it calls `protoc-gen-go`, which is a binary installed to `$GOBIN`. Now, `protoc-gen-go` also needs
flags passed to it but they can't be passed directly to `protoc`, so they're passed within the `--go_out` flag.
The final colon separates the sub-flags from the argument to `--go_out`.
 https://github.com/golang/protobuf/issues/239

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
