# TITLE

Synopsis 16: I/O

# VERSION

    Created: 12 Sep 2006

    Last Modified: 5 Nov 2014
    Version: 28

Many of these functions will work as in Perl 5, except we're trying to rationalize everything into roles.  For
now you can assume most of the important functions will automatically
be in the \* namespace.  However, with IO operations in particular,
many of them are really methods on an IO handle, and if there is a
corresponding global function, it's merely an exported version of
the method.
��Щ�����еĺܴ�һ���־����� Perl 5 ��һ��, ������������һ�ж�����. ��������Լ�����Щ��Ҫ�����Ĵ󲿷ֶ����Զ����� * ���ֿռ�. Ȼ��, ���� IO �������Ƚ�����, �����еĴ󲿷���ʵ���������� IO  ����ϵķ���, �����������ص�ȫ�ֺ���,  ��ֻ�����Ƿ����ĵ����汾.

# IO

## Overridable IO handles

In Perl 6, there are the _standard_ IO handles, and any number of overriding
inner filehandles for the same symbol.

�� Perl 6 ��, �� ��׼�� _standard_ IO ���, ����ͬ�ķ��Ż�������������д���ڲ����.

��׼��� _standard_ ��������Ϥ����������(����������).
��׼����� STDIN  ��� `$*IN`,
��׼����� STDOUT ��� `$*OUT`,
��׼����� STDERR ��� `$*ERR`.
�� Perl 6��, ��Щ���Ÿ���ش�����һ�ָ������һ�������ľ��.
��Ϊ������˼�Ǹ���������ص�.
��Щ���̵İ汾������ `PROCESS::` ���ֿռ���, ���Ԥ����  `GLOBAL::` ���ֿռ�ȫ���Ը���.

The _standard_ handles are our old familiar friends (with new names).
Standard input changed from STDIN to `$*IN`, standard output changed
from STDOUT to `$*OUT`, and standard error changed from STDERR to
`$*ERR`.  In Perl 6 these symbols represent more of a concept than
a given filehandle, since the meaning is contextually determined.
The process's version of these handles live in the `PROCESS::`
namespace, which is more global than the per-interpreter `GLOBAL::`
namespace.

��û����ʽ��ʹ�þ��ʱ, ��׼ IO ���������ݶ�̬����������.
���� `say` ������ӡ�� `$*OUT`, �� `note` ��ӡ�� `$*ERR`.
 `lines()` ������ `$*ARGFILES` ��ȡ����, ��û���ļ���ʱ, ��Ĭ�ϴ� `$*IN` ��ȡ����.
 �����κθ����Ķ�̬������( ������/�߳�/����/��������) �������¶�����Щ�������˼.
When no explicit filehandle is used, the standard IO operators are
defined in terms of the dynamic variables.  So the `say` function
prints to `$*OUT`, while `note` prints to `$*ERR`.  The `lines()`
term inputs from `$*ARGFILES` which defaults to `$*IN` in the absence of any
filenames.  So any given dynamic scope (interpreter,
thread, function or method call) may redefine the current meaning of
any of those filehandles within the dynamic scope of itself and of
its called routines.

����, ����֮, ��������дʱ:
    say "Howdy, world!"

the `say` function looks for the current meaning of `$*OUT`, and
takes the closest definition it can find in its callers.  If none
of the callers have overridden the definition, it looks in the
interpreter's `GLOBAL` namespace.  If the interpreter hasn't overridden
the meaning, it takes the meaning from `PROCESS`.  In essence, any
dynamic scope in Perl 6 is allowed to do IO redirection much like
a Unix shell does with its subprocesses, albeit with a different
syntax:

    {
        my $*OUT will leave *.close = open $newfile, :w;
        say "Written to $newfile";
    }
    # stdout reverts to outer scope's definition, and closed the file

In short:

              default handle
    routine   for sub form    purpose
    =======   ===========     =======
    print     $*OUT           string-based writing
    say       $*OUT           string-based writing
    get       $*ARGFILES      read a line (Str)
    lines     $*ARGFILES      read all lines (Str)
    words     $*ARGFILES      read all words (Str)
    read                      binary reading (Buf)
    write                     binary writing (Buf)

## Path Names and the .IO coercer

Path names �����ַ��� (`Str`).  ���� path ���ֵķ���, ��ʵ�᷵���ַ���.
ֻҪ����Ҫ����·����( ���� �ҳ�·����  `basename` �� `extension` ),
����� `.IO` ��������һ�� `IO::Path` ����:

    my $path = $filename.IO;

Ȼ�������ʹ�� `IO::Path` �����е��κη���, ���� `open`:

    my $handle = $newfile.IO.open(:w);

ע��, �����������˵, `open` ��������ʵ�����﷨��:

    my $handle = open $newfile, :w;

## $\*SPEC

The current system's path semantics are encapsulated in `$*SPEC` dynamic
variable.  It adheres to the `IO::Spec` interface, and is automatically
initialized for the current environment.  But like any dynamic variable,
can be overridden in a scope:

    { # Win32 path semantics in here
        my $*SPEC = IO::Spec::Win32;
        ...  # your code
    }
    # original path semantics here again

Please note that it does **not** need to be an instantiated object: the standard
`IO::Spec` subclasses only provide class methods, and therefore do not need an
instantiated object.  But that could be different for a very specific
third-party implementation of an `IO::Spec` class.

## $*CWD �� chdir()

��̬����  `$*CWD` ��һ������ǰ����Ŀ¼�� `IO::Dir` ����. ��ͨ��ʹ�� `chdir()` ����������, ������ָ����·���Ƿ���Ϊ�ļ��д���, ���ҿɷ��� (`-x`)

 object representing the current
working directory.  It is normally set with the `chdir()` function, which will
check whether the specified path exists as a directory and is accessible
(`-x`).

    chdir($dir);  # sets $*CWD of scope, usually PROCESS::<$CWD>

The `chdir()` function returns a `X::IO::Chdir` Failure if the path does
not exist, or is not a directory, or is not accessible.  Otherwise
returns a newly created `IO::Dir` object (which will be `True`).

`chdir()` ��������һ�� `X::IO::Chdir` ����, ���·�������ھͷ���
Please note that the path in `$*CWD` does not have any bearing on what the
underlying operating system's concept of a "current directory".  It is simply
the path that will prepended before any implicit or explicit relative paths,
and the default path that will be used when executing a sub-process.

To be changing `$*CWD` just for a given scope, you can use `indir()`:

    indir $dir, {
        ... your code in $dir ...
    };
    ... your code in $*CWD again ...

���������ʹ��һ����ʱ�� `$*CWD`, �洢 `chdir()`:

    {
        temp $*CWD = chddir($dir);
        ... your code in $dir ...
    }
    ... your code in $*CWD again ...

## $*TMPDIR �� tmpdir()

The dynamic variable `$*TMPDIR` is an `IO::Dir` object which points to the
system's directory for temporary files.  It can be set with the `tmpdir()`
function which will check whether the specified path exists as a directory and
has complete access (`+rwx`).

��̬���� `$*TMPDIR` ��һ�� `IO::Dir` ָ��ϵͳ��ʱ�ļ��еĶ���. 

    tmpdir($dir);  # sets $*TMPDIR of scope, usually PROCESS::<$TMPDIR>

To set a locally scoped version of `$*TMPDIR`, you can use `tmpdir()` with
a temporary `$*TMPDIR`:

    {
        temp $*TMPDIR = $tmpdir($dir);
        ... your code with $*TMPDIR being $dir ...
    }
    ... your code in original $*TMPDIR again ...

It will return a newly created `IO::Dir` object (which is `True`) or an
appropriate `Failure`.

The initialization of `$*TMPDIR` at startup is set depending on the OS you're
on.

## $\*HOME and homedir()

The dynamic variable `$*HOME` is an `IO::Dir` object which points to the
user's home directory.  It can be set with the `homedir()` function
which will check whether the specified path exists as a directory and is
completely accessible (`+rwx`).

    homedir($dir);  # sets $*HOME of scope, usually PROCESS::<$HOME>

To set a locally scoped version of `$*HOME`, you can use `homedir()` with a
temporary `$*HOME`:

    {
        temp $*HOME = homedir($dir);
        ... your code with $*HOME being $dir ...
    }
    ... your code in original $*HOME again ...

It will return a newly created `IO::Dir` object (which is `True`) or an
appropriate `Failure`.

The initialization of `$*HOME` at startup is set depending on the OS you're on.

## System dependent path semantics and IO::Spec

Each time an `IO::Path` object is created, the current `$*SPEC` will be
encapsulated in the object, to be used for all path related operations.

Of course, it is also possible to specify a specify a specific system's
path semantics module when creating an `IO::Path` object with the `:SPEC`
named parameter:

    my $SPEC = IO::Spec::Win32;
    my $path = $fileonNTFS.IO(:$SPEC);

or:

    my $path = $fileonNTFS.IO(:SPEC<Win32>); # auto-expand to IO::Spec::Win32

## ��������

The functions and classes that define most of the functionality for IO are
more thoroughly defined in S32-setting-library/IO.pod.  The main functions
used are listed in S29 with references to S32-setting-library/IO.pod.
An overview:

### ����

    print(@text)                  # print text on $*OUT
    say(@text)                    # print text + newline on $*OUT
    note(@text)                   # print text + newline on $*ERR
    dd($a,$b,$c)                  # tiny data dumper on $*ERR
    $line = prompt($message)      # print message on $*OUT, obtain next line

    $handle = open($path)         # open a file, return IO::Handle

    @paths = dir                  # paths (as IO::Path) in $*CWD
    @paths = dir($dir)            # paths (as IO::Path) in $dir

    $contents = slurp($handle)    # read all that's left of an opened filehandle
    $contents = slurp($filename)  # read all from given filename

    spurt($handle,$contents)      # write $contents to $handle
    spurt($filename,$contents)    # write $contents to $filename

    mkdir($dir)                   # create a directory
    rmdir($dir)                   # remove a directory
    mkpath($path)                 # create directory and parents as appropriate

    chdir($dir)                   # set $*CWD
    temp $*CWD = chdir($dir)      # set $*CWD for the current scope

    indir($dir, { ... })          # execute code with temporary $*CWD
        ...
    };

    tmpdir($dir)                  # set $*TMPDIR
    temp $*TMPDIR = tmpdir($dir)  # set $*TMPDIR for the current scope

    homedir($dir)                 # set $*HOME
    temp $*HOME = homedir($dir)   # set $*HOME for the current scope

    copy($from,$to)               # copy a file
    rename($from,$to)             # rename (move) a file on same physical storage
    move($from,$to)               # move (rename) a file to other storage
    unlink(*@files)               # remove one or more files
    chmod($permission,*@files)    # change permissions of one or more files

    link($target,$source)         # create a hard-link to a file
    symlink($target,$source)      # create a symbolic link to a file

### IO::Spec ��

The `IO::Spec` itself only has one method: `select`.  It takes an OS
descriptive name (usually something like what `$*DISTRO.name` gives) and
returns the type object of the appropriate `IO::Spec` subclass.

    my $*SPEC = IO::Spec.select("MSWin32");  # gives IO::Spec::Win32

Such a subclass should provide at least the following methods (in alphabetical
order):

    abs2rel        convert an absolute path into a relative one
    canonpath      return a canonical version of the given path
    catdir         concatenate directories
    catpath        create a path from volume/directories/filename
    curdir         the path to the current directory (usually '.')
    curupdir       test for matching curdir|updir
    devnull        the path to the bit bucket (on Unixy systems '/dev/null')
    extension      the extension of the path
    is-absolute    whether the path is absolute
    join           create a path from hash with volume/directories/filename
    PATH           %ENV<PATH> interpreted as paths
    rel2abs        convert a relative path into an absolute one
    rootdir        the path to the root directory (on Unixy systems '/')
    split          split a path into volume/directories/filename in hash
    splitdir       split directories
    splitpath      split a path into volume/directories/filename as Parcel
    tmpdir         path of the first writeable directory for temporary files
    updir          the path to the path directory (usually '..')

### IO::Path ��

    class IO::Path is Cool { }

The official way to create an `IO::Path` object is with the `new` method.
Apart from the `path` positional, it also takes optional `:SPEC` and
`CWD` named parameters.  The `.IO` coercer (which takes the same parameters
as `.new`) is the syntactic sugar that will most likely be used most often.

    my $io = $filename.IO;                  # current $*SPEC/$*CWD
    my $io = $filename.IO(:SPEC(*$SPEC));   # specific IO::SPEC
    my $io = $filename.IO(:SPEC(*$SPEC), :CWD($*CWD));

which would be the same as:

    my $io = IO::Path.new($filename);
    my $io = IO::Path.new($filename, :SPEC(*$SPEC));
    my $io = IO::Path.new($filename, :SPEC(*$SPEC), :CWD($*CWD));

If you only have filename components to start with, you can also create an
`IO::Path` object with the `:volume`, `:directory` and `:basename` named
parameters:

    my $io = IO::Path.new( :$volume, :$directory, :$basename );

The following file test methods are provided:

    r          is readable by effective uid/gid
    w          is writable by effective uid/gid
    x          is executable by effective uid/gid
    o          is owned by effective uid

    R          is readable by real uid/gid
    W          is writable by real uid/gid
    X          is executable by real uid/gid
    O          is owned by real uid

    e          exists
    s          Size of the $!path of $io in bytes
    z          has zero size (an empty file)

    f          is a plain file
    d          is a directory
    l          is a symbolic link
    L          path of symbolic link (readlink)
    p          is a named pipe (FIFO)
    S          is a socket
    b          is a block special file
    c          is a character special file

    u          has setuid bit set
    g          has setgid bit set
    k          has sticky bit set

To allow for easy chaining of file tests, there is an `.all` method that can
be fed the tests to be tried as a `Parcel` of strings.  The value returned
will be the first non-True value, or the final True value.

    say "rwx" if $io.all: <r w x>;

    if $io.all(<f r w x s>) -> $size {
        say "plain file with rwx of $size bytes";
    }

This is mostly handy when passing file tests as parameters between routines
and methods.  From a performance point of view, direct use of the methods,
like:

    if $io.f && $io.r && $io.w && $io.x && $io.s -> $size {
        say "plain file with rwx of $size bytes";
    }

or the smart match method:

    given $io {
        when :f :r :w :x {
            say "plain file with rwx of $_.s() bytes";
        }
    }

is probably faster.

These other methods are also provided (in alphabetical order):

    absolute       the absolute, canonical path
    accessed       last access time (if available)
    basename       the basename of the path
    changed        last (metadata) changed time
    chdir          change $*CWD if directory
    child          append basename to path, return new object for that
    chmod          change attributes of path
    copy           create a copy of file
    dir            files in path (if dir)
    directory      the directory part of the absolute path
    extension      the extension of the file
    is-absolute    is the (original) path absolute
    is-relative    is the (original) path relative
    lines          contents of file as lines
    mkdir          create directory
    modified       last modified time
    move           move (rename) to other storage
    open           attempt to open file, return IO::Handle
    parent         remove last portion of path, return new object for that
    pipe           attempt to open a pipe, return IO::Pipe
    pred           previous logical path, return new object for that
    relative       the relative path against CWD
    rename         rename (move) to other name
    resolve        follow symlinks to the real path, return new object for that
    rmdir          remove directory if empty directory
    slurp          obtain the contents of the file
    SPEC           the :SPEC at instantiation time
    spurt          set the contents of the file
    succ           next logical path, return new object for that
    unlink         remove file
    volume         the volume of the path (if any)
    words          contents of file as words

### IO::Handle ��

    class IO::Handle does IO { }

The `IO::Handle` object is usually **not** directly instantiated, but
with `open()` or `IO::Path.open`.  Nonetheless, you **can** create an
`IO::Handle` object with just a path:

    my $handle = IO::Handle.new($filename);
    my $handle = IO::Handle.new($filename, :SPEC(*$SPEC));
    my $handle = IO::Handle.new($filename, :SPEC(*$SPEC), :CWD($*CWD));

This does not interact with anything at all and will appear as if the file
has been `.close`d.  The `.open` method does interact with the file system:

    $handle.open;  # same as $handle = $filename.IO.open

It either returns True, or a `Failure` with additional information.

The other methods of the `IO::Handle` class are only valid **after** the
`.open` has been called successfully:

    close          close file handle, flush buffers
    encoding       set/return encoding of file handle
    eof            file pointer reached end of file
    fileno         file descriptor (usually a native integer)
    flush          flush buffers
    get            get next line from file
    getc           get next character from file
    ins            number of lines read
    IO             return new IO::Path of path of file
    lines          return rest of contents of file as lines
    opened         is the file open?
    p              the handle is a pipe
    path           the IO::Path of path of file, handles file tests
    print          write characters to file
    read           read bytes from file
    say            write characters + newline to file
    seek           move file pointer to given position
    slurp          return rest of contents of file
    spurt          write / append contents to file
    t              is the file a TTY (as a person looking?)
    tell           return position of file pointer
    words          return rest of contents of file as words
    write          write bytes to file

### Interplay between Roles and Classes

These classes and roles may cache and share pertinent information for better
performance.

# AUTHORS

    Largely, the authors of the related Perl 5 docs.
    Larry Wall <larry@wall.org>
    Mark Stosberg <mark@summersault.com>
    Tim Nelson <wayland@wayland.id.au>
    Daniel Ruoso <daniel@ruoso.com>
    Elizabeth Mattijsen <liz@dijkmat.nl>
