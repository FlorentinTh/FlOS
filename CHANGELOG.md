# Changelog

All notable changes to this project will be documented in this file.

This project adheres to [Semantic Versioning](http://semver.org/).

## [1.0.1] - 2019-07-31

### Added

- Several new missing packages and their dependencies :
  - ```avahi-daemon```, ```bash-completion```, ```fake-hwclock```, ```netcat-openbsd```, ```netcat-traditional```
- [Packaging](docs/50-bake-FlOS.md#13-package-the-image) the final image instructions in the documentation.
  
### Changed

- Use ```.xz``` archive instead of ```.tar.gz``` to reduce final size.
- Update the documentation.

### Removed

- Several useless packages and their dependencies :
  - ```apparmor```, ```busybox```, ```eatmydata```, ```krb5-locales```, ```libeatmydata1 pigz```

## 1.0.0 - 2019-07-30

- First public release.

[1.0.1]: https://github.com/florentinth/FlOS/compare/v1.0...v1.0.1
