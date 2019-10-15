# tiny-sandbox
User-friendly process isolation/sandboxing for Linux.

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

## Example usage
Run the following to create a no-login user named `hello-tsbox`:
```bash
$ sudo tsbox create hello-tsbox
```

In addition to a user named `hello-tsbox` being created, you'll notice
a file named `.tsbox/users/hello-tsbox.yaml` with contents like the following:

```yaml
persistent: false
mount:
  - /usr/bin
  - /usr/local/bin
  - /lib
  - /lib64
  - /dev
```

You can run a shell as `hello-tsbox`; it'll run in a chroot at `.tsbox/chroots/hello-tsbox`:
```bash
$ sudo tsbox run hello-tsbox bash -i
```

When the process exits, the chroot directory will be deleted (thanks to `persistent: false`).
You can entirely destroy the `hello-tsbox` user and its associated files by running:

```bash
$ sudo tsbox destroy -y hello-tsbox
```
