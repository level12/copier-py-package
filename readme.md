# Copier Python Package Template

A [copier](https://copier.readthedocs.io/en/stable/) template to create a Python package.


## Host Dependencies

To use a project generated by this template, you will need to have the following tools installed and
available on the path:

- copier with template extensions
  - `pipx install copier`
  - `pipx inject copier copier-templates-extensions`
- [mise](https://mise.jdx.dev/)
  - Set `WORKON_HOME` environment variable to location virtualenvs should be created
- [reqs](https://github.com/level12/reqs)

All other project dependencies should be managed by these tools:

- reqs/virtualenv for python deps
- Mise for everything else, e.g. terraform


## Usage

From GH repo (preferred):
```
copier copy --trust gh:level12/copier-py-package .../projects/some-new-pkg
```

Or, from local repo (mainly for local dev):
```
copier copy --trust .../copier-py-package .../projects/some-new-pkg
```

The method you choose (local vs. GH) affects the `_src` value stored in the copier answers file and
will be used by `copier update`.  Using a template stored on the local file system will save a
`_src` that may not be accurate for other users of `copier update`.  You can safely edit the local
reference to be the gh reference even though that answers file warns against editing it. Just make
sure the gh reference is accurate.

Then bootstrap...assuming mise activates when changing into pkg directory:

```
cd .../projects/some-new-pkg
mise run bootstrap
```

### Updates

To update a project derived from this repo:

* `hatch run copier:update`: latest tagged version in GitHub, OR
* `hatch run copier:update-head`: head of master in GitHub

The update should be pretty safe and only apply changes from the upstream repo that have happened
since this project was last updated.  Any conflicts with local changes to the project will show up
as git conflicts to be resolved.

## Features

- pyproject.toml package config
    - [Hatch](https://hatch.pypa.io/latest/) build backend w/ [support for requirements files](https://github.com/repo-helper/hatch-requirements-txt)
- [Ruff](https://docs.astral.sh/ruff/) linting & formatting
  - Enforce single quotes
  - Sane(ish) linting rules including safe fixes
- [mise](https://mise.jdx.dev/)
    - Manage [Python version](https://mise.jdx.dev/lang/python.html) and local dev
      [virtualenv activation](https://mise.jdx.dev/lang/python.html#automatic-virtualenv-activation)
    - Static [environment variables](https://mise.jdx.dev/environments.html)
    - Other tools when needed (e.g. npm, Terraform)
    - Project [tasks](https://mise.jdx.dev/tasks/)
- Versioning
  - date based by default (`mise run bump --help`)
  - bumping automatically commits, tags, and (by default) pushes
- [reqs](https://github.com/level12/reqs) for Python dependencies
- nox (tox alternative)
- pre-commit
- CircleCi config

Todo:

- env-config for environment profiles and 1password integration
- pre-commit
- badges
- review keg-app-cookiecutter

## Development

* Project tasks: `mise tasks`
* Build a demo project to test functionality: `mise run demo [--help]`
* CI uses a custom image built just for this project
  - See `compose.yaml` and related
  - Publish image changes to docker hub manually using: `mise run publish-ci-img`
* Simulate CI run locally: `mise run docker-nox`

### Versions & releases

Versions are date based.  Tools:

- Current version: `hatch version`
- Bump version based on date, tag, push: `mise run bump`
   - Options: `mise run bump -- --help`

There is no actual "release" for this project since it only lives on GitHub and no artifacts need
to be built.
