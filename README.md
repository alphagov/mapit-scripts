# MapIt-Scripts

## As of October 2022, Mapit is no longer used for location services within GDS, so these scripts are no longer needed - You should now use [Locations API](https://github.com/alphagov/locations-api) and/or [Local Links Manager](https://github.com/alphagov/local-links-manager) to provide the functionality that Mapit once provided.

For some more information about Mapit and things we should be aware of, please see
 * [the notes in the Opsmanual](https://github.gds/pages/gds/opsmanual/2nd-line/applications/mapit.html)
 * [the MapIt repo](https://github.com/alphagov/mapit)
 * [the MapIt wiki page](https://gov-uk.atlassian.net/wiki/display/TECH/MapIt).

You should also refer to the official [Mapit documentation](http://mapit.poplus.org/).

**Note that we currently use data imported using the `import-uk-onspd` script.**

Minimal requirements
====================

* Repo, from GitHub
* Packages needed are in conf/packages.ubuntu-trusty (+ unzip if you want to
  unzip data for import)
* PostGIS database
  - we run https://docs.djangoproject.com/en/dev/_downloads/create_template_postgis-debian.sh
  - Check to see if 27700/29902 changes are needed
    (see install_postgis() in https://github.com/mysociety/commonlib/blob/master/bin/install-site.sh )
* compass (if you want the front end)
  - bin/make_css
* conf/general.yml set up - DB config, COUNTRY: 'GB', AREA_SRID: 27700, SECRET_KEY: ...
* ./manage.py syncdb --noinput
  ./manage.py migrate mapit
* Run import-uk-onspd

Scripts
=======

import-uk-onspd
---------------
_This script needs to be updated to point to new datasets as they are released._

This script runs instructions similar to http://code.mapit.mysociety.org/import/uk/

It downloads Boundary-Line and ONSPD data, and OSNI (Northern Ireland) boundaries from the [mySociety cache](http://parlvid.mysociety.org/os/)

It loads fixtures, runs all the various importing scripts on this downloaded data,
and activates the generation. It also adds old ONS codes to the matching new GSS codes.

It downloads local authority slugs from the GOV.UK Frontend app and assigns them as Codes of type `govuk_slug` to the relevant Areas.

It does not use Code-Point data (unlike `import-uk`)

setup-and-import-uk
-------------------
_This script is not currently used for our production data set - please see
[import-uk-onspd](#import-uk-onspd)_

This script runs the mySociety install-site script to set up mapit (this does
things like install nginx, postgres, and so on, probably only useful if you're
e.g. running on an empty EC2 instance), changes the necessary bits of
configuration for running a UK instance (COUNTRY to "GB", AREA_SRID to 27700,
and DEBUG to False), sets up the database with a migrate, then runs import-uk.

Please see http://code.mapit.mysociety.org/install/install-script/ for more
information on the install script, http://code.mapit.mysociety.org/install/ for
more manual instructions.

import-uk
---------
_This script is not currently used for our production data set - please see
[import-uk-onspd](#import-uk-onspd)_

This script runs instructions similar to http://code.mapit.mysociety.org/import/uk/

It downloads the Boundary-Line from the [mySociety cache](http://parlvid.mysociety.org/os/), and all Code-Points
up to May 2015, which it merges together (in order to allow deleted
postcodes to also be imported). It uses the November 2015 ONSPD from the mySociety cache.

It loads fixtures, runs all the various importing scripts on this downloaded data,
and activates the generation. It also adds old ONS codes to the matching new GSS codes.

get-all-codepoints / merge-all-codepoints
-----------------------------------------
Called by import-uk, these fetch all Code-Points up to May 2015 from the
mySociety cache, and merge them together, respectively.

Notes
-----

Please note https://github.com/mysociety/mapit/wiki/NotesOnIDs regarding ID
usage now and in future.  As part of import-uk and import-uk-onspd we matches some old ONS codes to their GSS  equivalents.  However it's likely that new
releases of the data will increase the number of mismatches and we will need to update these scripts as more boundaries change.
