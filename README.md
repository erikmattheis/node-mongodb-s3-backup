# Node MongoDB / S3 Backup

This is a package that makes backing up your mongo databases to S3 simple.

In this fork,

* Command-line and chron functionality has been removed
* The module is to be used as a dependency to another module that
passes config options.
* The mongodump is passed --collection and --query options allowing
fine control over what from the database is backed up.

## Installation

    npm install mongodb_s3_backup

## Configuration

To configure the backup, you need to pass the sync() functions the configuration.

The configuration objects should be as follows::

```
var dbOptions = {
        "host": "localhost",
        "port": 27017,
        "username": false,
        "password": false,
        "db": "database_to_backup",
        "collection": "collection_to_backup",
        "query": "{}"
      };

var s3Options = {
        "key": "your_s3_key",
        "secret": "your_s3_secret",
        "bucket": "s3_bucket_to_upload_to",
        "destination": "/",
        "encrypt": true,
        "region": "s3_region_to_use"
      };
```

# Example use

```
var s3Backup = require('./node-mongodb-s3-backup'),
config = require('./config.json');

var lastMidnight = new Date()
  .setHours(0,0,0,0)
  .getTime();

var query = '{'
  + '\"timestamp\":{'
  + '\"\$gte\":' + lastMidnight + ','
  + '\"$lt\":' + (lastMidnight + (24*60*60*1000)) + '}}';

var dbOptions = config.mongodb;
  dbOptions.query = query;

s3Backup.sync(dbOptions, config.s3, function(err, result) {
    if (err) return console.error(err);
    console.info("Exiting:", result);
  });
```

All options in the "s3" object, except for desination, will be directly passed to knox, therefore, you can include any of the options listed [in the knox documentation](https://github.com/LearnBoost/knox#client-creation-options "Knox README").