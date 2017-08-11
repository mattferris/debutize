debutize
========

Automatically build Debian packages from source.

Overview
--------

*debutize* builds a Debian package (.deb) from your project source according to
the configuration defined in the `.debutize.conf` file in the root folder of
your project. Once configured, building a package is as simple as running:

```
# debutize ./
```

The resulting Debian package will be in the root folder of your project called
`{pkg_name}_{pkg_version}_{pkg_architecture}.deb`. So if you built version 2.0
of your project *fooproj* for amd64 platforms, your package would be called
`fooproj_2.0_amd64.deb`.

Configuring
-----------

### Metadata

The following package metadata options are available in the `.debutize.conf`:

- `pkg_name` - The package name *\*required*
- `pkg_version` - The package version *\*required*
- `pkg_depends` - The package's dependencies
- `pkg_architecture` - The (space-delimited) architectures of your package (can be `all` if architecture agnostic) *\*required*
- `pkg_section` - The section your package should be included in for the repository
- `pkg_priority` - The package priority
- `pkg_maintainer` - The package maintainer, usually "First Last <email@address.com>" *\*required\*
- `pkg_description` - The package description, if multiple lines, lines after the first must be prefixed with a space *\*required*

```
### .debutize.conf

# package metadata
pkg_name="fooproj"
pkg_version="2.0"
pkg_depends="baz"
pkg_architecture="amd64 i386"
pkg_section="admin"
pkg_priority="extra"
pkg_maintainer="John Smith <jsmith@example.com>"
pkg_description="fooproj is a utility for managing bar databases"
```

### Targets

Targets are space-delimited *source*:*destination* pairs that identify files in
your project source and their respective destinations in the package. Sources
can be files or directories. A source directory name with a trailing slash
copies the *contents* of the directory instead of the directory itself.

```
### .debutize.conf

# targets
targets="conf/:/etc/$pkg_name/ bin/:/usr/bin/ lib:/usr/lib/$pkg_name/"
```

### Config Files

Debian packages make special considerations for configuration files. Files
marked as configuration files won't be overwritten with package updates if
they've been modified. You can define a list of config files using the
`conffiles` option. Files are listed using space-delimited
*directory*:*pattern* pairs.

For example, if you wanted to specify all files in `/etc/fooproj` suffixed
with `*.conf` as config files, you would use

```
conffiles="/etc/fooproj:*.conf"
```

Even files in subdirectories of `/etc/fooproj` would be included. *debutize*
uses `find $directory -name "$pattern"` to locate files.

### Helpers

Helpers assist with the build process. Currently, only four helpers are
available: `systemd-stop.preinst`, `systemd-enable.postinst`, 
`systemd-start.postinst`, and `systemd-stop-disable.prerm`. These helpers
enable, start, stop, and disable the packages systemd unit respsectively.
Helpers are enabled using the `helpers` variable in `.debutize.conf`.

```
helpers="systemd-stop.preinst systemd-enable.postinst systemd-start.postinst systemd-stop-disable.prerm`"
```

For convenience, you can use a pattern syntax to simply enable all four.

```
helpers="systemd*"
```
