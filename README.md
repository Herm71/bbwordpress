# Blackbird Consulting WordPress
This is [Blackbird Consulting's](www.blackbirdconsult.com) base WordPress installation for use on all new projects. This git repository contains [WordPress](https://github.com/WordPress/WordPress) within it as a "fake" submodule, as demonstrated in [this post](http://debuggable.com/posts/git-fake-submodules:4b563ee4-f3cc-4061-967e-0e48cbdd56cb). In order to get a fresh version of WordPress from its own repo, simply ```cd wordpress```and perform a ```git pull```. 

Two plugins are also included, [WP-Sync-DB](https://github.com/wp-sync-db/wp-sync-db), a custom plugin that assists with database migration, and [Github-Updater](https://github.com/afragen/github-updater). WP-Sync-DB is not located in the main WordPress plugin repo, it is located on GitHub. Therefore, in order to update WP-Sync-DB, GitHub-Updater must also be installed.

## ```wp-content``` *outside* ```wordpress``` directory

This WordPress install is set up in a way that places the ```wp-content``` directory *outside* the main ```wordpress``` directory. This setup also places the ```index.php``` and ```wp-config.php``` files outside the ```wordpress``` directory. Thes files have been edited appropriately. This setup is oriented to be developed locally but pushed periodically to a staging environment. Thus, the ```wp-config.php``` has also been customized to allow inputting two sets of database credentials, one for your local development environment and one for the staging environment. 

###Changes made to the ```index.php``` file

The basic WordPress install ```index.php``` contains a reference that looks like this:

    /** Loads the WordPress Environment and Template */
    require( dirname( __FILE__ ) . '/wp-blog-header.php' );
    
This block has been changed, because it now resides outside the primary wordpress directory, to point to that directory:

    /** Loads the WordPress Environment and Template */
    require( dirname( __FILE__ ) . '/wordpress/wp-blog-header.php' );
    
###Changes made to the ```wp-config.php``` file

The basic WordPress install ```wp-config.php``` has been altered, too. Because we have moved both ```index.php``` and the ```wp-content``` folder outside the ```wordpress``` core installation, we must reference this change in ```wp-config.php```, which lets WordPress know where the core files are stored and that ```wp-content``` is located in the root directory. The following block has been added just below the opening comment block, just above the database credentials block:

####Tell WordPress where to go

    /** Let's WordPress know that core files are in "wordpress" directory, site served from root of project directory */
    define('WP_SITEURL', 'http://' . $_SERVER['SERVER_NAME'] . '/wordpress');
    define('WP_HOME',    'http://' . $_SERVER['SERVER_NAME']);

    /** Let's WordPress know that wp-content are in document root */
    define('WP_CONTENT_DIR', $_SERVER['DOCUMENT_ROOT'] . '/wp-content');
    define('WP_CONTENT_URL', 'http://' . $_SERVER['SERVER_NAME'] . '/wp-content');
    
####Tale of one database - the original ```wp-config.php``` file

The standard ```wp-config.php``` file contains the following block, which holds the database credentials:

    // ** MySQL settings - You can get this info from your web host ** //

    /** The name of the database for WordPress */
    define( 'DB_NAME', 'database_name' );

    /** MySQL database username */
    define( 'DB_USER', 'db_user' );

    /** MySQL database password */
    define( 'DB_PASSWORD', 'password' );

    /** MySQL hostname */
    define( 'DB_HOST', 'localhost' );
    

####Tale of two databases

The ```wp-config.php``` file in this repository has been changed enable two databases, one in the local development environment and one in the online staging environment. this file should be changed to the original once the staging environment is pushed as a live site.

    // ** MySQL settings - You can get this info from your web host ** //
    if ($_SERVER['REMOTE_ADDR']=='127.0.0.1') {
        define('WP_ENV', 'development');
    } else {
        define('WP_ENV', 'production');
    }

    // MySQL settings - You can get this info from your web host //
    if (WP_ENV == 'development') {
        define('DB_NAME', 'development_db_name');
        define('DB_USER', 'root');
        define('DB_PASSWORD', 'development_db_password');
        define('DB_HOST', 'localhost');
    } else {
        define('DB_NAME', 'staging_db_name');
        define('DB_USER', 'root');
        define('DB_PASSWORD', 'staging_db_password');
        define('DB_HOST', 'localhost');
    } 

##Cloning and Mirroring

###To clone this repository and include the submodules
**Issue the following ```git``` command:**

    blackbird@computer:~/htdocs$ git clone --recursive git@github.com:Herm71/bbwordpress.git yournewprojectdirectory

###To use this repository in a new project
**First, clone this repository into a temporary new bare repository on your local machine, like this:**

    blackbird@computer:~/htdocs$ git clone --bare --recursive git@github.com:Herm71/bbwordpress.git tempbbwordpress.git
    # Make a bare clone of the repository

**next, mirror this temporary clone to a new repository on your server**

    blackbird@computer:~/htdocs$ cd tempbbwordpress.git
    #Enter temporary local repository directory
        
    blackbird@computer:~/htdocs/tempbbwordpress.git$ git push --mirror https://github.com/exampleuser/new-repository.git
    # Mirror-push the temporary clone to a new repository

**next, remove the temporary clone repository**

    blackbird@computer:~/htdocs/tempbbwordpress.git$ cd ..
    #move up one level

    blackbird@computer:~/htdocs$ rm -rf tempbbwordpress.git
    #remove temporary repository

**Finally, create a new project directory on your development machine and clone the new repository into it:**

    blackbird@computer:~/htdocs$ mkdir newprojectdirectory
    # Create a new directory for your project

    blackbird@computer:~/htdocs$ cd newprojectdirectory
    # Enter the newly created directory

    blackbird@computer:~/htdocs/newprojectdirectory$ git clone --recursive https://github.com/exampleuser/new-repository.git
    # Remember to use recursive to include all submodules

#Changelog
Apple has a formalised version number structure based around the NumVersion struct, which specifies a one- or two-digit major version, a one-digit minor version, a one-digit "bug" (i.e. revision) version.

##0.2.1
- minor edit to wp-config. Created Private Branch for Blackbird user/pass, deleted from Master

##0.2.0
- wp-sync-db removed. Add via GitHub-Updater from now on

##0.1.0
- removed submodules, replaced with "fake" submodule for wordpress
- wp-sync-db added outside submodule framework. Will now be updated via GitHub-Updater
- github-updater added

##0.0.1
- constructed repo with wp-content outside of primary wordpress directory
- wordpress and wp-sync-db added as submodules