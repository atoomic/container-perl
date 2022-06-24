`container_perl`
====

This is a wrapper around perl to recreate the environment in `podman`. What it
does is,

* Get the version of perl on the host, map it to an OCI image name.
* Get the current `@INC` on the host.
* Prepare a container that maps the host's `@INC` directories to the container
* Clobbers the @INC of the container perl with just the mapped dirs from the
	host using [`libreplcae`](https://metacpan.org/pod/libreplace).
* `exec`'s a copy of a Perl using Podman in a container as above.

The source of inspiration of this was Brian Scannell's talk in The Perl
Conference 2022 on IDE and checking Perl syntax. In that talk Brian puts
forward two methods of dealing with the insecurity of checking perl syntax,
with `perl -c`

1. Trusting a project. Trust in this context would require you to audit the
	 code before testing the syntax. I doubt anyone will do this.
2. Executing the syntax checking on a remote machine, over SSH.

This approach uses podman to create a ephemeral container to syntax check Perl.

Notes
====

Currently, all the deps directory outside the directory are mounted read-only.
The working directory is mounted read-write.

Example
====

```sh
container-perl -E 'Hello World';
container-perl ./test.pl
```

For the purpose of the demo, `test.pl` outputs the UID. This will change when
run inside and outside of `container-perl` because user namespaces allow the
perl running in the namespace to think it's root. While invoking this file with
regular perl will show it as the UID of the user.
