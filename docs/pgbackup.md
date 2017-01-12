# pgbackup Project Specific Notes

## Openstack Deployment
If you want to deploy the pgbackup release under  an Openstack environment your need to follow the steps bellow.


Be sure to first target your BOSH Director:
```sh
bosh target $BOSH_HOST
```
Now clone this release and cd into the directory:
```sh
git clone https://.../pgbackup-boshrelease.git
cd pgbackup-boshrelease
```
If you intend on using a final release upload it like so:
```sh
bosh upload release releases/pgbackup-2.yml
```

Next edit the manifest file provided with the release (manifest/postgres-backup.yml).

This release aims to support both v1 and v2 manifest. Currently, only Openstack templates are offered by default, 
support for other Iaas are welcome.

You'll need to edit the manifest of the deployment to amend. You may choose to download the manifest from bosh director, or modify a version you saved into you version control.
```sh
bosh deployment your-bosh-deployment.yml
```

Add to the list of known `releases` :

```yaml
releases:
- name: pgbackup
  version: latest
```

Add the pgbackup property.

```yaml
properties:
# ... 
  pgbackup:
```

Now, for every `instances: ` entry you wish to collocate this release with under `jobs:` add the following in the `templates: ` section:

```yaml
  - name: pgbackup
    release: pgbackup
```

Now you can deploy,

```yaml
bosh -n deploy
```

The schedule property use the same syntax as the cron jobs under linux system.
##### how to configure the backup job  : https://help.ubuntu.com/community/CronHowto
