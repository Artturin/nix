# Environment Variables

To use Nix, some environment variables should be set. In particular,
`PATH` should contain the directories `prefix/bin` and
`$XDG_STATE_HOME/nix/profile/bin` (or `~/.nix-profile/bin` if
`~/.nix-profile` exists). The first directory contains the Nix tools
themselves, while `$XDG_STATE_HOME/nix/profile` is a symbolic link to
the current *user environment* (an automatically generated package
consisting of symlinks to installed packages). The simplest way to set
the required environment variables is to include the file
`prefix/etc/profile.d/nix.sh` in your `~/.profile` (or similar), like
this:

```bash
source prefix/etc/profile.d/nix.sh
```

# `NIX_SSL_CERT_FILE`

If you need to specify a custom certificate bundle to account for an
HTTPS-intercepting man in the middle proxy, you must specify the path to
the certificate bundle in the environment variable `NIX_SSL_CERT_FILE`.

If you don't specify a `NIX_SSL_CERT_FILE` manually, Nix will install
and use its own certificate bundle.

Set the environment variable and install Nix

```console
$ export NIX_SSL_CERT_FILE=/etc/ssl/my-certificate-bundle.crt
$ sh <(curl -L https://nixos.org/nix/install)
```

In the shell profile and rc files (for example, `/etc/bashrc`,
`/etc/zshrc`), add the following line:

```bash
export NIX_SSL_CERT_FILE=/etc/ssl/my-certificate-bundle.crt
```

> **Note**
> 
> You must not add the export and then do the install, as the Nix
> installer will detect the presence of Nix configuration, and abort.

## `NIX_SSL_CERT_FILE` with macOS and the Nix daemon

On macOS you must specify the environment variable for the Nix daemon
service, then restart it:

```console
$ sudo launchctl setenv NIX_SSL_CERT_FILE /etc/ssl/my-certificate-bundle.crt
$ sudo launchctl kickstart -k system/org.nixos.nix-daemon
```

## Proxy Environment Variables

The Nix installer has special handling for these proxy-related
environment variables: `http_proxy`, `https_proxy`, `ftp_proxy`,
`no_proxy`, `HTTP_PROXY`, `HTTPS_PROXY`, `FTP_PROXY`, `NO_PROXY`.

If any of these variables are set when running the Nix installer, then
the installer will create an override file at
`/etc/systemd/system/nix-daemon.service.d/override.conf` so `nix-daemon`
will use them.

## XDG Base Directory

Nix conforms to the [XDG Base
Directory](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)
standard, and uses the following environment variables to determine
locations of various state and configuration files:

- `$XDG_STATE_HOME` (assumed to be `~/.local/share` if not set): used
  for storing state, such as the current user environment and
  channels, repl history, and allowed flake settings.
- `$XDG_CACHE_HOME` (assumed to be `~/.cache` if not set): used for
  storing cache, such as flake evaluation cache and eval-time fetcher
  results.
