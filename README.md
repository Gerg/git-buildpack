# git-buildpack

Buildpack for pushing apps to Cloud Foundry directly from a git repo.

**Status:** Highly Experimental

## Usage

To push an app without having to clone it locally:
```
$ export TMPDIR=`mktemp -d`
$ pushd $TMPDIR
$ touch hello
$ cf push git -b https://github.com/Gerg/git-buildpack.git -b ruby_buildpack --no-start
$ cf set-env git GIT_SHA cebed1e0169eb70d1843a0f028fdb0197ba2bdca
$ cf set-env git GIT_REPO https://github.com/cloudfoundry-samples/ruby-sample-app.git
$ cf restage git
$ popd
$ rm -rf $TMPDIR
```

## Why This is a Bad Idea

The "proper" implementation of something like this would probably be a git (or
generic source control) package instead of a buildpack. As you can see above, we
have to push up a package with a useless file in it. The git buildpack deletes
the contents of the package and then clones the repo.

This is also built on pretty shaky ground, because buildpacks are not supposed
to modify the `build` directory, which this one does with wild abandon:

> The supply script must not modify anything outside of the deps/index
> directory. Staging may fail if such modification is detected.
- Source: https://docs.cloudfoundry.org/buildpacks/understand-buildpacks.html
