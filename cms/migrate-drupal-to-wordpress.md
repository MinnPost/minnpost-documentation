# Migrating Drupal (6) to WordPress

We're working to determine realitic ways of migrating our Drupal 6 site to WordPress, and would like to document at least some of this process.

** Any technical things should be run locally. Assume nothing has been run on a remote or production server unless it says otherwise. **

## Options

### Another Cup of Coffee

This site offers a PHP app (no longer maintained) and a Python command-line app that users can download, install, and run themselves. Documentation is a bit light, but the features are high.

Python app: https://github.com/anthonylv/pyD2W
Explanation: http://anothercoffee.net/drupal-to-wordpress-migration-explained/
Notes: http://anothercoffee.net/drupal-to-wordpress-migration-notes/

This site also offers migration as a paid service. To get a full, free estimate, you need to provide:

1. A migration specification document that shows what to migrate.
2. A MySQL dump file of the database that can be analyzed.

### Blondish.net

This article - http://blondish.net/how-to-convert-drupal-to-wordpress/ - offers a conversion process that "will work for converting up to the latest version of Drupal to the latest version of WordPress, as of January 1, 2015."

### Toodlepip

This one - http://toodlepip.co.uk/2014/migrating-drupal-6-wordpress-part-2-drupal-wordpress/ - is explicitly for Drupal 6. At the time it was migrating to WordPress 3.8, but it's likely it would still work for higher versions.

### Underdog of Perfection

This - http://blog.room34.com/archives/4530 - is Drupal 6 to WordPress 3.0. The above piece was derived at least partly from this one, so it's possibly redundant. However it does have a downloadable file, and that could be promising.


## Results

### Another Cup of Coffee

For the Python app (once it is installed), a lot more setup is required, and a lot of tweaking as well. The app cannot run out of the box. To run the app, some tables from a default WordPress install have to be added to one of the custom SQL files. However, you can't just place a SQL dump from an empty WordPress; some of the tables are created further in the script and it will fail if they already exist.

Once we got it to successfully run, it migrated a lot of records over about an hour (into the acc_ set of tables). We tried copying them all into an empty database, but this failed to run as a WordPress install. Several tables (notably wp_options) were still empty.

Suspicion is that maybe it ignores the setting for the WordPress database entirely, and doesn't migrate anything into it.

It's posssible we might have been able to supply it with a migration script as well as all the preparation scripts, and then it might have migrated into the existing WordPress database.

### Blondish.net

We tested this with Drupal 6, and it was able to do a lot of migrating, but not all and we're a bit unclear what issues there were.

### Toodlepip

We've not tried this yet.

### Underdog of Perfection

We've not tried this yet.