# Scientific Python: guide, cookie, & sp-repo-review

## Cookie

[![Actions Status][actions-badge]][actions-link]
[![GitHub Discussion][github-discussions-badge]][github-discussions-link]
[![Live ReadTheDocs][rtd-badge]][rtd-link]

[![PyPI version][pypi-version]][pypi-link]
[![PyPI platforms][pypi-platforms]][pypi-link]

A [copier][]/[cookiecutter][] template for new Python projects based on the
Scientific Python Developer Guide. What makes this different from other
templates for Python packages?

- Lives with the [Scientific-Python Development Guide][]: Every decision is
  clearly documented and every tool described, and everything is kept in sync.
- Eleven different backends to choose from for building packages.
- Optional VCS versioning for most backends.
- Template generation tested in GitHub Actions using nox.
- Supports generation with [copier][], [cookiecutter][], and [cruft][].
- Supports GitHub Actions if targeting a `github.com` url (the default), and
  adds experimental GitLab CI support otherwise.
- Includes several compiled backends using [pybind11][], with wheels produced
  for all platforms using [cibuildwheel][].
- Provides [`sp-repo-review`][pypi-link] to evaluate existing repos against the
  guidelines, with a WebAssembly version integrated with the guide. All checks
  cross-linked.
- Follows [PyPA][] best practices and regularly updated.

Be sure you have read the [Scientific-Python Development Guide][] first, and
possibly used them on a project or two. This is _not_ a minimal example or
tutorial. It is a collection of useful tooling for starting a new project using
cookiecutter, or for copying in individual files for an existing project (by
hand, from `{{cookiecutter.project_name}}/`).

During generation you can select from the following backends for your package:

1. [hatch][]: This uses hatchling, a modern builder with nice file inclusion,
   extendable via plugins, and good error messages. **(Recommended for pure
   Python projects)**
2. [flit][]: A modern, lightweight [PEP 621][] build system for pure Python
   projects. Replaces setuptools, no MANIFEST.in, setup.py, or setup.cfg. Low
   learning curve. Easy to bootstrap into new distributions. Difficult to get
   the right files included, little dynamic metadata support.
3. [pdm][]: A modern, less opinionated all-in-one solution to pure Python
   projects supporting standards. Replaces setuptools, venv/pipenv, pip, wheel,
   and twine. Supports [PEP 621][].
4. [whey][]: A modern [PEP 621][] builder with some automation options for Trove
   classifiers. Development seems to be stalled, possibly. (No VCS versioning)
5. [poetry][]: An all-in-one solution to pure Python projects. Replaces
   setuptools, venv/pipenv, pip, wheel, and twine. Higher learning curve, but is
   all-in-one. Makes some bad default assumptions for libraries. The only one
   with a non-standard pyproject.toml config.
6. [setuptools621][setuptools]: The classic build system, but with the new
   standardized configuration.
7. [setuptools][]: The classic build system. Most powerful, but high learning
   curve and lots of configuration required.
8. [pybind11][]: This is setuptools but with an C++ extension written in
   [pybind11][] and wheels generated by [cibuildwheel][].
9. [scikit-build][]: A scikit-build (CMake) project also using pybind11, using
   scikit-build-core. **(Recommended for C++ projects)**
10. [meson-python][]: A Meson project also using pybind11. (No VCS versioning)
11. [maturin][]: A [PEP 621][] builder for Rust binary extensions. (No VCS
    versioning) **(Recommended for Rust projects)**

Currently, the best choice is probably hatch for pure Python projects, and
scikit-build (such as the scikit-build-core + pybind11 choice) for binary
projects.

#### To use (modern copier version)

Install `copier` and `copier-templates-extensions`. Using [pipx][], that's:

```bash
pipx install copier
pipx inject copier copier-templates-extensions
```

Now, run copier to generate your project:

```bash
copier copy gh:scientific-python/cookie <pkg> --trust
```

(`<pkg>` is the path to put the new project. If copier is old, use `--UNSAFE`
instead of `--trust`)

You will get a nicer CLI experience with answer validation. You will also get a
`.copier-answers.yml` file, which will allow you to perform updates in the
future.

> Note: Add `--vcs-ref=HEAD` to get the latest version instead of the last
> tagged version; HEAD always passes tests (and is what cookiecutter uses).

#### To use (classic cookiecutter version)

Install cookiecutter, ideally with `brew install cookiecutter` if you use brew,
otherwise with `pipx install cookiecutter` (or prepend `pipx run` to the command
below, and skip installation). Then run:

```bash
cookiecutter gh:scientific-python/cookie
```

If you are using cookiecutter 2.2.3+, you will get nice descriptions for the
options like copier!

#### To use (classic cruft version)

You can also use [cruft][], which adds the ability update to cookiecutter
projects. Install with `pipx install cruft` (or prepend `pipx run` to the
command below, and skip installation). Then run:

```bash
cruft create gh:scientific-python/cookie
```

#### Post generation

Check the key setup files, `pyproject.toml`, and possibly `setup.cfg` and
`setup.py` (pybind11 example). Update `README.md`. Also update and add docs to
`docs/`.

There are a few example dependencies and a minimum Python version of 3.8, feel
free to change it to whatever you actually need/want. There is also a basic
backports structure with a small typing example.

#### Contained components:

- GitHub Actions runs testing for the generation itself
  - Uses nox so cookie development can be checked locally
- GitHub actions deploy
  - C++ backends include cibuildwheel for wheel builds
  - Uses PyPI trusted publisher deployment
- Dependabot keeps actions up to date periodically, through useful pull requests
- Formatting handled by pre-commit
  - No reason not to be strict on a new project; remove what you don't want.
  - Includes MyPy - static typing
  - Includes Black - standardizing formatting
  - Includes strong Ruff-based linting and autofixes
    - Replaces Flake8, isort, pyupgrade, yesqa, pycln, and dozens of plugins
  - Includes spell checking
- An pylint nox target can be used to run pylint, which integrated GHA
  annotations
- A ReadTheDocs-ready Sphinx docs folder and `[docs]` extra
- A test folder and pytest `[test]` extra
- A noxfile is included with a few common targets

Setuptools only:

- Setuptools controlled by `setup.cfg` and a nominal `setup.py`.
  - Using declarative syntax avoids needless boilerplate that is often wrong
    (like incorrectly handling the encoding when opening a README).
  - Easier to adapt to PEP 621 eventually.
  - Any actual logic can sit in setup.py and be clearly separate from simple
    metadata.
- Versioning handled by `setuptools_scm`
  - You can easily switch to manual versioning, but this avoids duplicating the
    version as git tags and in the source, and versions _every_ commit uniquely,
    sidestepping some caching problems.
- `MANIFEST.in` checked with check-manifest
- `setup.cfg` checked by setup-cfg-fmt

#### For developers:

You can test locally with [nox][]:

```console
# See all commands
nox -l

# Run a specific check
nox -s "lint(setuptools)"

# Run a noxfile command on the project noxfile
nox -s "nox(whey)" -- docs
```

If you don't have `nox` locally, you can use [pipx][], such as `pipx run nox`
instead.

#### Other similar projects

[Hypermodern-Python][hypermodern] is another project worth checking out with
many similarities, like great documentation for each feature and many of the
same tools used. It has a slightly different set of features, and has a stronger
focus on GitHub Actions - most our guide could be adapted to a different CI
system fairly easily if you don't want to use GHA. It also forces the use of
Poetry (instead of having a backend selection), and doesn't support compiled
projects. It currently dumps all development dependencies into a shared
environment, causing long solve times and high chance of conflicts. It also does
not use pre-commit the way it was intended to be used. It also has quite a bit
of custom code.

#### History

A lot of the guide, cookiecutter, and repo-review started out as part of
[Scikit-HEP][]. These projects were merged, generalized, and combined with the
[NSLS-II][] guide during the 2023 Scientific-Python Developers Summit.

<!-- prettier-ignore-start -->

[actions-badge]: https://github.com/scientific-python/cookie/workflows/CI/badge.svg
[actions-link]: https://github.com/scientific-python/cookie/actions
[cibuildwheel]: https://cibuildwheel.readthedocs.io
[cookiecutter]: https://cookiecutter.readthedocs.io
[copier]: https://copier.readthedocs.io
[cruft]: https://cruft.github.io/cruft
[flit]: https://flit.readthedocs.io/en/latest/
[github-discussions-badge]: https://img.shields.io/static/v1?label=Discussions&message=Ask&color=blue&logo=github
[github-discussions-link]: https://github.com/scientific-python/cookie/discussions
[hatch]: https://github.com/ofek/hatch
[hypermodern]: https://github.com/cjolowicz/cookiecutter-hypermodern-python
[maturin]: https://maturin.rs
[meson-python]: https://meson-python.readthedocs.io
[nox]: https://nox.thea.codes/en/stable/
[nsls-ii]: https://nsls-ii.github.io/scientific-python-cookiecutter/
[pdm]: https://pdm.fming.dev
[pep 621]: https://www.python.org/dev/peps/pep-0621
[pipx]: https://pypa.github.io/pipx/
[poetry]: https://python-poetry.org
[pybind11]: https://pybind11.readthedocs.io
[pypa]: https://www.pypa.io
[pypi-link]: https://pypi.org/project/sp-repo-review/
[pypi-platforms]: https://img.shields.io/pypi/pyversions/sp-repo-review
[pypi-version]: https://badge.fury.io/py/sp-repo-review.svg
[rtd-badge]: https://readthedocs.org/projects/scientific-python-cookie/badge/?version=latest
[rtd-link]: https://scientific-python-cookie.readthedocs.io/en/latest/?badge=latest
[scikit-build]: https://scikit-build.readthedocs.io
[setuptools]: https://setuptools.readthedocs.io
[whey]: https://whey.readthedocs.io

<!-- prettier-ignore-end -->

---

## sp-repo-review

<!-- sp-repo-review -->

`sp-repo-review` provides checks based on the [Scientific-Python Development
Guide][] at [scientific-python/cookie][] for [repo-review][].

This tool can check the style of a repository. Use like this:

```bash
pipx run 'sp-repo-review[cli]' <path to repository>
```

This will produce a list of results - green checkmarks mean this rule is
followed, red x’s mean the rule is not. A yellow warning sign means that the
check was skipped because a previous required check failed. Some checks will
fail, that’s okay - the goal is bring all possible issues to your attention, not
to force compliance with arbitrary checks. Eventually there might be a way to
mark checks as ignored.

For example, `GH101` expects all your action files to have a nice `name:` field.
If you are happy with the file-based names you see in CI, you should feel free
to simply ignore this check (just visually ignore it for the moment, a way to
specify ignored checks will likely be added eventually).

All checks are mentioned at least in some way in the [Scientific-Python
Development Guide][]. You should read that first - if you are not attempting to
follow them, some of the checks might not work. For example, the guidelines
specify pytest configuration be placed in `pyproject.toml`. If you place it
somewhere else, then all the pytest checks will be skipped.

This was originally developed for [Scikit-HEP][] before moving to Scientific
Python.

## Other ways to use

You can also use GitHub Actions:

```yaml
- uses: scientific-python/cookie@<version>
```

Or pre-commit:

```yaml
- repo: https://github.com/scientific-python/cookie
  rev: <version>
  hooks:
    - id: sp-repo-review
```

If you use `additional_dependencies` to add more plugins, like
`validate-pyproject`, you should also include `"repo-review[cli]"` to ensure the
CLI requirements are included.

## List of checks

<!-- prettier-ignore-start -->

<!-- [[[cog
import itertools

from repo_review.processor import collect_all
from repo_review.checks import get_check_url, get_check_description
from repo_review.families import get_family_name

collected = collect_all()
print()
for family, grp in itertools.groupby(collected.checks.items(), key=lambda x: x[1].family):
    print(f'### {get_family_name(collected.families, family)}')
    for code, check in grp:
        url = get_check_url(code, check)
        link = f"[`{code}`]({url})" if url else f"`{code}`"
        print(f"- {link}: {get_check_description(code, check)}")
    print()
]]] -->

### General
- [`PY001`](https://learn.scientific-python.org/development/guides/packaging-simple#PY001): Has a pyproject.toml
- [`PY002`](https://learn.scientific-python.org/development/guides/packaging-simple#PY002): Has a README.(md|rst) file
- [`PY003`](https://learn.scientific-python.org/development/guides/packaging-simple#PY003): Has a LICENSE* file
- [`PY004`](https://learn.scientific-python.org/development/guides/packaging-simple#PY004): Has docs folder
- [`PY005`](https://learn.scientific-python.org/development/guides/packaging-simple#PY005): Has tests folder
- [`PY006`](https://learn.scientific-python.org/development/guides/style#PY006): Has pre-commit config
- [`PY007`](https://learn.scientific-python.org/development/guides/tasks#PY007): Supports an easy task runner (nox or tox)

### PyProject
- [`PP002`](https://learn.scientific-python.org/development/guides/packaging-simple#PP002): Has a proper build-system table
- [`PP003`](https://learn.scientific-python.org/development/guides/packaging-classic#PP003): Does not list wheel as a build-dep
- [`PP301`](https://learn.scientific-python.org/development/guides/pytest#PP301): Has pytest in pyproject
- [`PP302`](https://learn.scientific-python.org/development/guides/pytest#PP302): Sets a minimum pytest to at least 6
- [`PP303`](https://learn.scientific-python.org/development/guides/pytest#PP303): Sets the test paths
- [`PP304`](https://learn.scientific-python.org/development/guides/pytest#PP304): Sets the log level in pytest
- [`PP305`](https://learn.scientific-python.org/development/guides/pytest#PP305): Specifies xfail_strict
- [`PP306`](https://learn.scientific-python.org/development/guides/pytest#PP306): Specifies strict config
- [`PP307`](https://learn.scientific-python.org/development/guides/pytest#PP307): Specifies strict markers
- [`PP308`](https://learn.scientific-python.org/development/guides/pytest#PP308): Specifies useful pytest summary
- [`PP309`](https://learn.scientific-python.org/development/guides/pytest#PP309): Filter warnings specified

### Documentation
- [`RTD100`](https://learn.scientific-python.org/development/guides/docs#RTD100): Uses ReadTheDocs (pyproject config)
- [`RTD101`](https://learn.scientific-python.org/development/guides/docs#RTD101): You have to set the RTD version number to 2
- [`RTD102`](https://learn.scientific-python.org/development/guides/docs#RTD102): You have to set the RTD build image
- [`RTD103`](https://learn.scientific-python.org/development/guides/docs#RTD103): You have to set the RTD python version

### GitHub Actions
- [`GH100`](https://learn.scientific-python.org/development/guides/gha-basic#GH100): Has GitHub Actions config
- [`GH101`](https://learn.scientific-python.org/development/guides/gha-basic#GH101): Has nice names
- [`GH102`](https://learn.scientific-python.org/development/guides/gha-basic#GH102): Auto-cancel on repeated PRs
- [`GH103`](https://learn.scientific-python.org/development/guides/gha-basic#GH103): At least one workflow with manual dispatch trigger
- [`GH104`](https://learn.scientific-python.org/development/guides/gha-wheels#GH104): Use unique names for upload-artifact
- [`GH200`](https://learn.scientific-python.org/development/guides/gha-basic#GH200): Maintained by Dependabot
- [`GH210`](https://learn.scientific-python.org/development/guides/gha-basic#GH210): Maintains the GitHub action versions with Dependabot
- [`GH211`](https://learn.scientific-python.org/development/guides/gha-basic#GH211): Do not pin core actions as major versions
- [`GH212`](https://learn.scientific-python.org/development/guides/gha-basic#GH212): Require GHA update grouping

### MyPy
- [`MY100`](https://learn.scientific-python.org/development/guides/style#MY100): Uses MyPy (pyproject config)
- [`MY101`](https://learn.scientific-python.org/development/guides/style#MY101): MyPy strict mode
- `MY102`: MyPy show_error_codes deprecated
- [`MY103`](https://learn.scientific-python.org/development/guides/style#MY103): MyPy warn unreachable
- [`MY104`](https://learn.scientific-python.org/development/guides/style#MY104): MyPy enables ignore-without-code
- [`MY105`](https://learn.scientific-python.org/development/guides/style#MY105): MyPy enables redundant-expr
- [`MY106`](https://learn.scientific-python.org/development/guides/style#MY106): MyPy enables truthy-bool

### Pre-commit
- [`PC100`](https://learn.scientific-python.org/development/guides/style#PC100): Has pre-commit-hooks
- [`PC110`](https://learn.scientific-python.org/development/guides/style#PC110): Uses black or ruff-format
- [`PC111`](https://learn.scientific-python.org/development/guides/style#PC111): Uses blacken-docs
- [`PC140`](https://learn.scientific-python.org/development/guides/style#PC140): Uses mypy
- [`PC160`](https://learn.scientific-python.org/development/guides/style#PC160): Uses codespell
- [`PC170`](https://learn.scientific-python.org/development/guides/style#PC170): Uses PyGrep hooks (only needed if RST present)
- [`PC180`](https://learn.scientific-python.org/development/guides/style#PC180): Uses prettier
- [`PC190`](https://learn.scientific-python.org/development/guides/style#PC190): Uses Ruff
- [`PC191`](https://learn.scientific-python.org/development/guides/style#PC191): Ruff show fixes if fixes enabled
- [`PC901`](https://learn.scientific-python.org/development/guides/style#PC901): Custom pre-commit CI message

### Ruff
- [`RF001`](https://learn.scientific-python.org/development/guides/style#RF001): Has Ruff config
- [`RF002`](https://learn.scientific-python.org/development/guides/style#RF002): Target version must be set
- [`RF003`](https://learn.scientific-python.org/development/guides/style#RF003): src directory specified if used
- [`RF101`](https://learn.scientific-python.org/development/guides/style#RF101): Bugbear must be selected
- [`RF102`](https://learn.scientific-python.org/development/guides/style#RF102): isort must be selected
- [`RF103`](https://learn.scientific-python.org/development/guides/style#RF103): pyupgrade must be selected
- `RF201`: Avoid using deprecated config settings
- `RF202`: Use (new) lint config section

<!-- [[[end]]] -->

[repo-review]: https://repo-review.readthedocs.io
[scientific-python development guide]: https://learn.scientific-python.org/development
[scientific-python/cookie]: https://github.com/scientific-python/cookie
[scikit-hep]: https://scikit-hep.org

<!-- prettier-ignore-end -->
