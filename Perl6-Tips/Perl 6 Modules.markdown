 Perl 6 Documentation
Search 

Perl 6 Modules
Creating and Distributing Modules Creating and Distributing Modules

If you've written a Perl 6 module and would like to share it with the community, we'd be delighted to have it listed in the Perl 6 modules directory . :)

For now, the process requires that you use git for your module's version control.

The instructions herein require that you have a github account so that your module can be shared from its github repository.

To share your module then, do the following:


Create a project directory named after your module. For example, if your module is Vortex::TotalPerspective, then create a project directory named Vortex-TotalPerspective . This project directory name will also be used as the github repo name.


Make your project directory look like this:
Vortex-TotalPerspective/
|-- lib
|   `-- Vortex
|       `-- TotalPerspective.pm
|-- LICENSE
|-- META.info
|-- README.md
`-- t
    `-- basic.t

If your project contains other modules that help the main module do its job, they should go in your lib directory like so:
lib
`-- Vortex
    |-- TotalPerspective.pm
    `-- TotalPerspective
        |-- FairyCake.pm
        `-- Gargravarr.pm


The README.md file is a markdown-formatted text file, which will later be automatically rendered as html by github.


Regarding the LICENSE file, if you have no other preference, you might just use the same one that Rakudo Perl 6 uses. Just copy/paste the raw form of its license into your own LICENSE file.


If you don't yet have any tests, you can leave out the t directory and basic.t file for now. For more info on how to write tests (for now), you might have a look at how other modules use Test. It's quite similar to Perl 5's Test::More.


To document your modules, use Perl 6 Pod markup inside your modules. Module documentation is most appreciated, and will be especially important once the Perl 6 module directory (or some other site) begins rendering Pod docs as html for easy browsing. [1]


Make your META.info file look something like this:
    {
        "name" : "Vortex::TotalPerspective",
        "version" : "0.1.0",
        "description" : "Wonderful simulation to get some perspective.",
        "author" : "
Your Name
",
        "depends" : [ ],
        "source-url" : "git://github.com/
you
/Vortex-TotalPerspective.git"
    }


For choosing a version numbering scheme, perhaps use "major.minor.patch" (see the spec on versioning for further details). If the version number doesn't matter to you or your users right now, you can just put in an asterisk (*) for the version.


Put your project under git version control if you haven't done so already.


Once you're happy with your project, create a repo for it at github. See github's help docs if necessary. Your github repo should be named the same as your project directory. Immediately after creating the github repo, github shows you how to configure your local repo to know about your github repo.


Push your project to github.


Ping someone on IRC (#perl6 at freenode) to either show you how to add your module to the ecosystem , or else ask them if they could please add it for you.


That's it! Thanks for contributing to the Perl 6 community!

If you'd like to try out installing your module, use the panda module installer tool which is included with Rakudo Star Perl 6:
panda install Vortex::TotalPerspective


This will download your module to its own working directory ( ~/.panda ), build it there, and install the module into ~/.perl6 .

To use Vortex::TotalPerspective from your scripts, just write use Vortex::TotalPerspective , and your Perl 6 implementation will know where to look for the module file(s).
[↑] Note, described above is a minimal project directory. If your project contains scripts that you'd like distributed along with your module(s), put them in a bin directory. If you have extra docs (in addition to the Pod docs in your module(s)), create a doc directory for them. If you'd like a graphical logo to appear next to your module at the module directory, create a logotype directory and put into it a C<logo_32x32.png> file. At some point, you might also consider adding CONTRIBUTORS, NEWS, TODO, or other files.


Generated on 2014-03-22T13:18:49-0400 from the sources at perl6/doc on github . This is a work in progress to document Perl 6, and known to be incomplete. Your contribution is appreciated.

The Camelia image is copyright 2009 by Larry Wall.
