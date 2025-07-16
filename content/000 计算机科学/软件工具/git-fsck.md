## SYNOPSIS

_git fsck_ [--tags] [--root] [--unreachable] [--cache] [--no-reflogs]
	 [--[no-]full] [--strict] [--verbose] [--lost-found]
	 [--[no-]dangling] [--[no-]progress] [--connectivity-only]
	 [--[no-]name-objects] [<object>…​]

## DESCRIPTION

Verifies the connectivity and validity of the objects in the database.

It stands for File System ChecK. The name is taken from the Unix [`fsck`](http://linux.die.net/man/8/fsck) command, which is used to validate a file system.

While Git is not technically a file system, it can be used analogously and the command name is a metaphor on this.