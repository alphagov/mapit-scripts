For some more information about Mapit and things we should be aware of, please see [the notes on the dev wiki](https://github.com/alphagov/wiki/wiki/Mapit)

Please find some hopefully helpful notes on top of the [Mapit documentation](http://code.mapit.mysociety.org/).

Minimal requirements
====================

* Repo, from github
* Packages needed are in conf/packages.ubuntu-precise (+ unzip if you want to
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

setup-and-import-uk
-------------------
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
This script runs instructions similar to http://code.mapit.mysociety.org/import/uk/
It downloads the October 2015 Boundary-Line from the mySociety cache, and all Code-Points
up to May 2015, which it merges together (in order to allow deleted
postcodes to also be imported). It uses the November 2015 ONSPD from the mySociety cache.

It loads fixtures, runs all the various importing scripts on this downloaded data,
and activates the generation. It also adds old ONS codes to the matching new GSS codes.

get-all-codepoints / merge-all-codepoints
-----------------------------------------
Called by import-uk, these fetch all Code-Points up to May 2015 from the
mySociety cache, and merge them together, respectively.

import-uk-onspd
---------------
This script is very similar to `import-uk` with the following exceptions:

1. It does not download Code Point Open at all and uses the ONSPD only imports we've added to [our mapit fork](http://github.com/alphagov/mapit)
2. It downloads the latest ONSPD (Aug 2015 currently) direct from the ONS

Notes
-----

Please note https://github.com/mysociety/mapit/wiki/NotesOnIDs regarding ID
usage now and in future.  As part of import-uk and import-uk-onspd we matches some old ONS codes to their GSS  equivalents.  However it's likely that new
releases of the data will increase the number of mismatches and we will need to update these scripts as more boundaries change.
