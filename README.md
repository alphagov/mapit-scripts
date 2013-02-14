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
* Run import-uk

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
It downloads the May 2012 Boundary-Line from the mySociety cache, and all Code-Points
up to November 2012, which it merges together (in order to allow deleted
postcodes to also be imported). It gets ONSPD from the ONS website.

It loads fixtures, runs all the various importing scripts on this downloaded data,
and activates the generation. It also adds old ONS codes to the matching new GSS codes.

get-all-codepoints / merge-all-codepoints
-----------------------------------------
Called by import-uk, these fetch all Code-Points up to November 2012 from the
mySociety cache, and merge them together, respectively.


Notes
-----

Please note https://github.com/mysociety/mapit/wiki/NotesOnIDs regarding ID
usage now and in future. The import matching old ONS codes to their GSS
equivalents that import-uk does will not match the four councils listed below,
as their GSS codes have changed since the old ONS codes. If you need to match
current council GSS IDs to what their ONS codes were in the past, you will
need to add the ONS codes below to their new GSS codes.

* East Dunbartonshire Council has GSS code S12000045. It used to be S12000009
  which is equivalent to old code 00QL.

* Glasgow City Council has GSS code S12000046. It used to be S12000043 which is
  equivalent to old code 00QS.

* St Albans Borough Council has GSS code E07000240. It used to be E07000100
  which is equivalent to old code 26UG.

* Welwyn Hatfield Borough Council has GSS code E07000241. It used to be
  E07000104 which is equivalent to old code 26UL.

