# Changelog

This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html)
and [human-readable changelog](https://keepachangelog.com/en/1.0.0/).

## Unreleased
### Changed
* Updated default Restic version to `'0.9.6'`
### Fixed
* SFTP Link correctly renders in MD

## 0.2.1
### Fixed
* Crontab entries are now created correctly (@dnmvisser)
* Molecule now correctly runs lint (@mleutenegger)

## 0.2.0
### Added
* S3 Support

## 0.1.5
### Changed
* Path generation for `forget` task now checks if `src` is actually filled and not only defined.

### Added
* `prune` option for automatic pruning during forget


## 0.1.4
### Changed
* `--keep-tags` tags are correctly applied to the forget command
* Backup policies now also check if the respective variable actually contains a value

### Added
* Added a template which only adds credentials for use with `source` on linux distros

## 0.1.3
### Changed
* Creating script dir before templating out scripts

## 0.1.2
### Changed
* build sucessfully completes

## 0.1.1
### Changed
* `restic_url` is now set in defaults

## 0.1.0
### Added
* initial release
