Travis CI Homebrew custom bottle script
=======================================

This script is to help with the scenario where you are compiling for OSX with
Travis and you need to install software with Homebrew, but the default options
in the brew's bottle won't work for you.

In this situation, you could compile the software from scratch, but this takes
a long time on every push and wastes valuable Travis resources.

Instead, you can use this script to create a Homebrew Bottle (precompiled
version) of the software you need, then you can simply install the bottle in
your OSX build and there's no need to compile anything.

If you have a Mac you could do all this locally, but this script is for those
without access to OSX.  It uses Travis CI itself to compile the dependency once
and upload it as a Github release, where it is easily available for other
builds to use.

This script is currently set to brew and bottle `boost` with the `--c++11`
parameter, as the official `boost` bottle has been compiled without C++11
support.

Using this code
---------------

To make use of this script:

  1. Fork this repository on Github.

  2. In your Travis CI profile, use the button to resync to your Github
     account.

  3. Find the newly forked repo in the Travis CI list and enable it.

  4. Make a backup of `.travis.yml`.

  5. Run `travis setup releases --force`.  Use your own Github credentials to
     generate a new OAuth token, so Travis can create Github releases for the
     repository.

  6. Put the new encrypted OAuth token into the `.travis.yml` backup.

  7. Overwrite `.travis.yml` with the updated backup and delete the backup.

  8. Update `.travis.yml` to compile the bottle you need.

  9. Commit the change and set a tag, e.g. `git tag boost-1.60-cxx11`

  10. Push the change to Github, making sure to push the tag also
      (`git push origin master --tags`).

  11. Watch Travis CI build the brew, bottle it, then upload it as a Github
      release.

Using the bottle
----------------

To use the bottle generated by this process, add the following to the
`.travis.yml` for the project that needs to use the bottle.

    before_install:
      - if [[ "$TRAVIS_OS_NAME" == "osx" ]]; then wget 'https://github.com/<link-to-release>'; fi
      - if [[ "$TRAVIS_OS_NAME" == "osx" ]]; then brew install *.bottle.tar.gz; fi
