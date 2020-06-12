#### Database Backup Script for MySQL and Dumping to Amazon S3

You can automate the creation of backup and storing it to Amazon S3 within a few minutes. Below bullets brief about what you are going to learn in this part of the article:

* Create a script that automates the MySQL backup directory creation
* Upload/sync the backups with Amazon S3
* Cron will run this command every day (to back up)

### Step 1: Generating a shell script which will dump the MySQL database

```shell
cd ~
mkdir scripts
cd scripts
nano db_backup.sh
```

Copy and paste the script below to it

```shell
#!/bin/bash
DIR=`date +%d-%m-%y`
DEST=~/db_backups/$DIR
mkdir $DEST

mysqldump -h mysql_hostname -u mysql_user  -p"mysql_password" database_name > dbbackup.sql
```

Now chomd the script to allow it to for execution

```shell
chmod +x ~/scripts/db_backup.sh
```

### Step 2: Creating the shell script which sync the backups with Amazon S3

```
nano db_sync.sh
```

Copy and paste the script below to it

```
#!/bin/bash
/usr/local/bin/aws s3 sync ~/db_backups s3://my-bucket-name
```

Now chmod the script to allow it for execution

```
chmod +x ~/scripts/db_sync.sh
```

### Step 3: Creating the folder in Amazon S3 for the database dumps

```
cd ~
mkdir db_backups
```

### Step 4: Time to configure the AWS CLI

Before installing the AWS CLI you need to install`python-pi`. Type the following commands:

```
apt-get update
apt-get -y install python-pip
curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
```

[Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

Type the following command:

```
pip install awscli
```

### Step 5: Time to set up AWS key & Secret

[Configuration and credential file settings](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

![](https://simplebackups.io/images/uploads/image2.png)

```
cd ~
mkdir .aws
nano ~/.aws/config
```

Paste in `key_id `and `secret_access_key` as shown below

```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

### Step 6: Set up the Cron (to automate the process)

```
crontab -e
```

Paste the below commands at the bottom to automate the process

```
0 0 * * * ~/scripts/db_backup.sh # take a backup every midnight
0 2 * * * ~/scripts/db_sync.sh # upload the backup at 2am
```

This way the backup script will run and also sync with Amazon S3 daily.

### Conclusion

Hence, by using these scripts you can achieve 3 goals:

1. Creating the database backup via a shell script
2. uploading the dump to Amazon S3
3. also automating this process using Cron.

- - -

*Have you tried [SimpleBackups.io](https://simplebackups.io/?ref=giblo)yet?*

*SimpleBackups will save you a lot of time setting up scripts and ensuring they run without problems. It will alert you when things go wrong, and allows you store your backups on many cloud storage services like Google, DigitalOcean, Wasabi, Dropbox, and more…*

![](https://simplebackups.io/images/uploads/simplebackups-bringstorage.png)
