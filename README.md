# tiny-sandbox
User-friendly process isolation/sandboxing for Linux.

*Note:* This is intended for one-off command-line applications, and is untested with desktop
apps.

## About
`tiny-sandbox` is a tool that manages unprivileged users on a Linux system.
It is intended for servers where completely untrusted code will run along other
untrusted code. Processes are run in bare-minimum chroot jails, given no
privileges, and have other restrictions applied to effectively prevent them
from tampering with the system, let alone discovering that they are not the only
user on the system at all.

For example, if you are running multiple user's code on the machine, and cannot afford
to have them mess with another's code, `tiny-sandbox` is a very simple way to spin up
isolated environments without much headache.

## Prerequisites
You must have the following installed:
* `/bin/bash`
* `chroot`
* `ip`
* `unshare`

## Example usage
Run the following to create a no-login user named `hello-tsbox`:
```bash
$ sudo tsbox create hello-tsbox
```

In addition to a user named `hello-tsbox` being created, you'll notice
a file named `.tsbox/users/hello-tsbox.yaml` with contents like the following:

```yaml
env: {}
network:
  share: false
persistent: false
mount:
  - /bin
  - /usr/bin
  - /usr/local/bin
  - /usr/lib
  - /lib
  - /lib64
  - /dev
```

Before you can run programs, you must `mount` the necessary directories. This command
can be run multiple times, and is idempotent:

```bash
$ sudo tsbox mount hello-tsbox
```

You can run a shell as `hello-tsbox`; it'll run in a chroot at `.tsbox/chroots/hello-tsbox`:
```bash
$ sudo tsbox shell hello-tsbox
```

You can run any process within the jail by calling `tsbox run`. In fact, `tsbox shell` is
just an alias for running `/bin/bash`:

```bash
$ sudo tsbox run hello-tsbox whoami
```

You can entirely destroy the `hello-tsbox` user and its associated files by running:

```bash
$ sudo tsbox destroy hello-tsbox
```

## Configuration
* `env`: Optional environment variables to pass to the process. Parent
environment variables are not shared.
* `memory_limit`: Sets a limit on memory usage. Defaults to `128000000` (128MB).
* `mount`: Specifies directories to mount into the chroot, read-only.
* `mount_rw`: Specifies directories to mount into the chroot, read-write.
* `network`:
  * `share`: If `False` (default), then the child process will run in its own
  network namespace.
* `persistent`: Currently unused.