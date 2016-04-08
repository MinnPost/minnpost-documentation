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

Once it successfully ran, it migrated a lot of records into the acc_ set of tables. We tried copying them all into an empty database, but this failed to run as a WordPress install until we imported wp_options from another install. Further results below.

** It's posssible we might have been able to supply a migration script as well as all the preparation scripts, and then it might have migrated the tables into the existing WordPress database, rather than needing to move them.

- Custom fields:
- Pages: 
- Categories:
- Subcategories:
- Tags:
- Media:
- Other content types: 
- URLs:
- Sidebar items:
- Comments:
- Users:

#### Presumably successful tables:

1. wp_comments
2. wp_links (since we don't have anything to put in there)
3. wp_posts (somewhat unclear on what percentage was actually migrated, though)
4. wp_term_relationships
5. wp_term_taxonomy
6. wp_terms
7. wp_users (partial; I think it worked for all commenting users)
8. wp_usermeta (same as above)
9. wp_postmeta (empty; but it seems maybe it doesn't need anything)
10. wp_termmeta (same as above)
11. wp_commentmeta (same as above)

#### Presumably unsuccessful tables:

1. wp_options (empty)

#### Result numbers: 

wp_commentmeta (0)
wp_comments (~155,027)
wp_links (0)
wp_options (0)
wp_postmeta (0)
wp_posts (~31,811)
wp_term_relationships (120,564)
wp_term_taxonomy (7,503)
wp_termmeta (0)
wp_terms (7,503)
wp_usermeta (108)
wp_users (~14,069)

### Blondish.net

We tested this with Drupal 6, and it was able to do a lot of migrating. It's unclear so far what core article content was not migrated.

- Custom fields:
- Pages: 
- Categories: migrated, but not relationships to posts.
- Subcategories:
- Tags:
- Media:
- Other content types: 
- URLs:
- Sidebar items:
- Comments:
- Users:

#### Issues inserting posts:

1. Field 'to_ping' doesn't have a default value (set to Allow NULL)
2. Field 'pinged' doesn't have a default value (set to Allow NULL)
3. Field 'post_content_filtered' doesn't have a default value (set to Allow NULL)
4. Column 'post_name' cannot be null (set to Allow Null)
5. Remember to do it for each content type we want
6. On authors, Column 'post_author' cannot be null (set to Allow NULL)
7. Don't forget to run the weird thing on wp-config.php and then remove it after.

#### Issues inserting comments:

1. Data truncated for column 'comment_parent' at row 183487 (field was previously a BIGINT(20); try making it VARCHAR(255))
2. Data too long for column 'comment_author_url' at row 252111 (field was previously VARCHAR(200); try making it VARCHAR(255))

#### Result numbers: 

wp_commentmeta (0)
wp_comments (~169,050)
wp_links (0)
wp_options (106)
wp_postmeta (0)
wp_posts (~38,545)
wp_term_relationships (129,723)
wp_term_taxonomy (6,722)
wp_termmeta (0)
wp_terms (6,722)
wp_usermeta (3,910)
wp_users (~1,947)


### Toodlepip

We've not tried this yet.

### Underdog of Perfection

We tested this with Drupal 6, and it was able to do a lot of migrating; similar results to Blondish. It's unclear so far what core article content was not migrated.

- Custom fields:
- Pages: 
- Categories: migrated, but not relationships to posts.
- Subcategories:
- Tags:
- Media:
- Other content types: 
- URLs:
- Sidebar items:
- Comments:
- Users:

#### Issues inserting posts:

1. Field 'to_ping' doesn't have a default value (set to Allow NULL)
2. Field 'pinged' doesn't have a default value (set to Allow NULL)
3. Field 'post_content_filtered' doesn't have a default value (set to Allow NULL)
4. Column 'post_name' cannot be null (set to Allow Null)
5. Remember to do it for each content type we want
6. On authors, Column 'post_author' cannot be null (set to Allow NULL)
7. Don't forget to run the weird thing on wp-config.php and then remove it after.

#### Issues inserting comments:

1. Data truncated for column 'comment_parent' at row 183487 (field was previously a BIGINT(20); try making it VARCHAR(255))
2. Data too long for column 'comment_author_url' at row 252111 (field was previously VARCHAR(200); try making it VARCHAR(255))

#### Issues with categories:

1. Write a custom query to get departments (which are nodes) into the terms table.
2. wp_term_taxonomy description doesn't have a default value (set to Allow NULL)

#### Result numbers: 

wp_commentmeta (0 maybe; however it has an error even trying to load it)
wp_comments (~161,718)
wp_links (0)
wp_options (105)
wp_postmeta (0)
wp_posts (~38,239)
wp_term_relationships (140,830)
wp_term_taxonomy (1)
wp_termmeta (0)
wp_terms (6,787)
wp_usermeta (3,909)
wp_users (~1,947)