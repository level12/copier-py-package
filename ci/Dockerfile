# This is the image used by .circleci/config.yml
# Its customized to avoid the mise install on every test run.

arg UBUNTU_VERSION=22.04
from cimg/base:current-${UBUNTU_VERSION} as muvis

copy --from=jdxcode/mise:latest /usr/local/bin/mise /usr/local/bin/
copy --from=ghcr.io/astral-sh/uv:latest /uv /uvx /usr/local/bin/

copy --chown=circleci:circleci .python-version .
run uv python install

#### TARGET: nox  ####
from muvis as nox

# TARGET NOTE: lots of copying instead of bind mounting a volume to avoid docker writing files as
# root to the host file system.  Could have used a .dockerignore that matches the .gitignore and
# likely ended up with similar results.  But I (RLS) think explicit test failures from not copying
# something into the image are easier to troubleshoot than letting the .dockerignore get outdated
# and then have the image be larger.

# Prep for the reqs install just below
# copy --chown=circleci:circleci mise.toml .
# copy --chown=circleci:circleci requirements requirements

# This duplicates what is in the ci-tests task, but by doing it here we cache the results in the
# image which saves runtime whenever the reqs haven't changed, which is often.  uv still runs but
# everything is up to date, so the run only takes tens of ms.
# run mise exec -- uv pip install -U uv
# run mise exec -- uv pip install -r requirements/ci.txt

# Similar to the above, save pre-commit environments in image for faster runtimes
copy --chown=circleci:circleci .pre-commit-config.yaml .
copy --chown=circleci:circleci .git .git

# run mise exec -- pre-commit install-hooks

copy --chown=circleci:circleci \
    copier.yaml \
    extensions.py \
    hatch.toml \
    noxfile.py \
    pyproject.toml \
    ruff.toml \
    .
copy --chown=circleci:circleci mise-tasks mise-tasks
copy --chown=circleci:circleci src src
copy --chown=circleci:circleci template template

run uv sync --group nox

entrypoint ["/home/circleci/bin/uv", "run", "nox"]
cmd []
