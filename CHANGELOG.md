# Changelog

This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html)
and [human-readable changelog](https://keepachangelog.com/en/1.0.0/).

## Unreleased
### Added
* Config option to exclude files
* Contributing guide

## [0.2.6] 2020-06-05
### Changed
* Password now use `regex_escape()` filter in templates

## [0.2.5] - 2020-06-02
### Fixed:
* AWS credentials are now supplied to the initialising step
* Cronjob step does not fail if `scheduled` parameter is not set on repo
* init respects `'config already initialized'` string

## [0.2.4] - 2020-06-01
### Added
* `aws_default_region` for repos
## 0.2.3 - 2020-05-18
### Fixed
* extraction path is actually generated
## [0.2.2]
### Changed
* Updated default Restic version to `'0.9.6'`
### Fixed
* Backup scripts use bash (@maciekmm)
* SFTP Link correctly renders in MD

## [0.2.1]
### Fixed
* Crontab entries are now created correctly (@dnmvisser)
* Molecule now correctly runs lint (@mleutenegger)

## [0.2.0]
### Added
* S3 Support

## [0.1.5]
### Changed
* Path generation for `forget` task now checks if `src` is actually filled and not only defined.

### Added
* `prune` option for automatic pruning during forget


## [0.1.4]
### Changed
* `--keep-tags` tags are correctly applied to the forget command
* Backup policies now also check if the respective variable actually contains a value

### Added
* Added a template which only adds credentials for use with `source` on linux distros

## [0.1.3]
### Changed
* Creating script dir before templating out scripts

## [0.1.2]
### Changed
* build sucessfully completes

## [0.1.1]
### Changed
* `restic_url` is now set in defaults

## 0.1.0
### Added
* initial release


[Unreleased]: https://github.com/arillso/ansible.restic/compare/0.2.6...HEAD
[0.2.6]: https://github.com/arillso/ansible.restic/compare/0.2.5...0.2.6
[0.2.5]: https://github.com/arillso/ansible.restic/compare/0.2.4...0.2.5
[0.2.4]: https://github.com/arillso/ansible.restic/compare/0.2.3...0.2.4
[0.2.3]: https://github.com/arillso/ansible.restic/compare/0.2.2...0.2.3
[0.2.2]: https://github.com/arillso/ansible.restic/compare/0.2.1...0.2.2
[0.2.1]: https://github.com/arillso/ansible.restic/compare/0.2.0...0.2.1
[0.2.0]: https://github.com/arillso/ansible.restic/compare/0.1.5...0.2.0
[0.1.5]: https://github.com/arillso/ansible.restic/compare/0.1.4...0.1.5
[0.1.4]: https://github.com/arillso/ansible.restic/compare/0.1.3...0.1.4
[0.1.3]: https://github.com/arillso/ansible.restic/compare/0.1.2...0.1.3
[0.1.2]: https://github.com/arillso/ansible.restic/compare/0.1.1...0.1.2
[0.1.1]: https://github.com/arillso/ansible.restic/compare/0.1.0...0.1.1
