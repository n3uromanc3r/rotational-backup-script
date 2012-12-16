 ______         __          __   __                     __ 
|   __ \.-----.|  |_.---.-.|  |_|__|.-----.-----.---.-.|  |
|      <|  _  ||   _|  _  ||   _|  ||  _  |     |  _  ||  |
|___|__||_____||____|___._||____|__||_____|__|__|___._||__|
                                                           
 ______              __                  _______             __         __   
|   __ \.---.-.----.|  |--.--.--.-----. |     __|.----.----.|__|.-----.|  |_ 
|   __ <|  _  |  __||    <|  |  |  _  | |__     ||  __|   _||  ||  _  ||   _|
|______/|___._|____||__|__|_____|   __| |_______||____|__|  |__||   __||____|
                                |__|                            |__|

============================================================================
1.  How to Install
2.  Additional Requirements
--> 2.1 How to Setup Postfix
--> 2.2 How to Setup Passwordless SSH Login
3.  Configuration
4.  First Run
5.  Automation
============================================================================


1. How to Install
____________________________________________________________________________

Once you have the file downloaded, you must make it executable:

	chmod +x rotational-backup-script

Then move it into your path:

	sudo mv rotational-backup-script /usr/local/bin/



2.  Additional Requirements
____________________________________________________________________________

This script requires the following tools:

	rsync, mailutils, postfix

To install them on a Debian based system, use:

	sudo apt-get install rsync postfix mailutils



2.1 How to Setup Postfix
____________________________________________________________________________

If the installation of postfix skipped over the configuration, we can reconfigure it using the following on a Debian based system:

	sudo dpkg-reconfigure postfix

Then select the following from each screen (on a typical setup):

	-General type of mail configuration: "Internet Site"
	-System mail name: Should be the address of the site which sends the email reports eg;"foobar.com"
	-Root and postmaster mail recipient: "admin" or anything you want really!
	-Other destinations to accept mail for (blank for none): "localhost.localdomain, , localhost" can also contain your hostname
	-Force synchronous updates on mail queue? "Yes" or "No"
	-Local networks: "127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128"
	-Mailbox size limit (bytes): "0"
	-Local address extension character: "+"
	-Internet protocols to use: "all"



2.2 How to Setup Passwordless SSH Login
____________________________________________________________________________

This script is designed to be part of an automated process.  So it is essential that the ssh connection used in the script does not require a user to enter a password during authentication.

To setup passwordless login, we need to generate an ssh key pair (private and public), and then copy the public key to the remote server.

To generate the key pair, run this command on your local machine:

	ssh-keygen

...when prompted, you can hit enter to select the default location for the key, and then hit enter without entering a passphrase too.

Once the key pair has bee generated, we then copy the public key to the remote server:

	ssh-copy-id foo@example.com

We should now be able to login to the remote server without the need for a password by issuing the following command:

	ssh foo@example.com



3.  Configuration
____________________________________________________________________________

There are a number of variables to configure in the script:

REMOTE_LOCATION -- The remote location, must have a trailing slash
REMOTE_USER	-- The remote users name
REMOTE_SERVER -- The remote server IP address
BACKUP_LOCATION -- The local backup directory, must NOT have a trailing slash
NUMBER_OF_BACKUPS_TO_KEEP -- The number of backups to keep in rotation
COMPANY_NAME -- The company/organisation whose owns the backups
EMAIL_ADDRESS -- The email address to receive backup reports upon success or failure

eg:

REMOTE_LOCATION="/home/foo/bar/"
REMOTE_USER="foo"
REMOTE_SERVER="000.000.000.000"
BACKUP_LOCATION="/home/baz/qux"
NUMBER_OF_BACKUPS_TO_KEEP="30"
COMPANY_NAME="Example"
EMAIL_ADDRESS="anon@example.com"



4.  First Run
____________________________________________________________________________

The first time it is run, you must use the -i flag to initialize the backup directory structure:

	rotational-backup-script -i

During the initialization, you have the opportunity to add files and folders to an exclusion list for rsync.  These files/folders will be ignored during backups.

Once setup is complete, all subsequent backups can be executed using:

	rotational-backup-script

The configured email recipient should receive an email after the backup is complete.  Logs will also be created in the configured location.



5.  Automation
____________________________________________________________________________

To set this up as a cron job, type:

	crontab -e

Then add the following line to do daily backups at midnight:

	0     0     *     *     *         /usr/bin/local/rotational-backup-script

For more information about cron, see https://en.wikipedia.org/wiki/Cron


