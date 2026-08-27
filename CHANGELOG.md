# Changelog

## [2.3.2](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.3.1...v2.3.2) (2026-08-27)


### Fixes

* pass files/content via raw files to handle large files that would otherwise go over OS's ARG_MAX ([#28](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/28)) ([68c3917](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/68c39172977d87d24d17b025c00fa33e04ff69ae))

## [2.3.1](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.3.0...v2.3.1) (2026-07-29)


### Fixes

* skip tests in addition to examples, this is to skip .tf files that might exist in tests ([#26](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/26)) ([290f356](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/290f356495f1e40e10e87158327df897293d8af7))

## [2.3.0](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.2.0...v2.3.0) (2026-07-28)


### Features

* add terraform validate step against examples ([#23](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/23)) ([08dd29d](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/08dd29da3868da8805b0242337b9e7df38eae56a))


### Fixes

* allow more generic passing of tf token ([#25](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/25)) ([c450cbc](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/c450cbce76033d55201191f6514bedf88cd41ab0))

## [2.2.0](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.1.3...v2.2.0) (2026-07-27)


### Features

* set TF_TOKEN_app_terraform_io to pull any nested private modules ([#21](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/21)) ([ec9d9bf](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/ec9d9bf354ea1e224440c2c1fcb243f237ae5d13))

## [2.1.3](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.1.2...v2.1.3) (2026-07-16)


### Fixes

* juli 2026: bump all versions ([#18](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/18)) ([5268081](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/5268081ce3a5b7cef276c73c038b1f4f3c7c1e6c))

## [2.1.2](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.1.1...v2.1.2) (2026-05-29)


### Fixes

* bump actions to use node24 ([#16](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/16)) ([efc0729](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/efc0729b093efb1f0e0aab81b0983f6f158292c4))

## [2.1.1](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.1.0...v2.1.1) (2026-05-12)


### Fixes

* guard tflint examples loop against missing or empty examples dir ([#14](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/14)) ([6bfff9c](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/6bfff9ca6ceffbfef9d5ba0dd4e453a46c78a557))

## [2.1.0](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v2.0.0...v2.1.0) (2026-05-11)


### Features

* run tflint in examples ([#12](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/12)) ([245666b](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/245666b07440ef62b6b0ff1e81baa20a59e864ba))

## [2.0.0](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v1.1.0...v2.0.0) (2026-04-09)


### ⚠ BREAKING CHANGES

* add terraform provider support ([#10](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/10))

### Features

* add terraform provider support ([#10](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/10)) ([28cab53](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/28cab53e0a6b4cc0da8ead7414b7ce8a6c52c8da))

## [1.1.0](https://github.com/schubergphilis-ep/github-terraform-workflows/compare/v1.0.1...v1.1.0) (2026-04-08)


### Features

* add release-please for automated versioning and releases ([#8](https://github.com/schubergphilis-ep/github-terraform-workflows/issues/8)) ([d64b76f](https://github.com/schubergphilis-ep/github-terraform-workflows/commit/d64b76f78ef2ac4cb6fa7374322c1f48ab2dae18))
