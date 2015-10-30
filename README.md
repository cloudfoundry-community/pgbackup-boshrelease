# pgbackup BOSH release

## License

MIT, see LICENSE file.

## Usage: Configuration & Delpoyment

We will walk through an example of using with Cloud Foundry.

Be sure to first target your BOSH Director:
```sh
bosh target $BOSH_HOST
```

If you are using bosh-lite you target like so:
```sh
bosh target 192.168.50.4 lite
```

Now clone this release and cd into the directory:
```sh
git clone https://.../pgbackup-boshrelease.git
cd pgbackup-boshrelease
```

If you intend on using a final release upload it like so:
```sh
bosh upload release releases/pgbackup-1.yml
```

Next download your manifest file for the deployment targeted so we can edit it and add the release.

```sh
mkdir -p ~/workspace/manifests
bosh download manifest pgbackup-development ~/workspace/manifests/pgbackup.yml
bosh deployment ~/workspace/manifests/pgbackup.yml
```

Alternatively, you can make your manifest. For example to prepare a manifest for
bosh-lite (warden) using the 'centos' stemcell we would do the following:

```sh
STEMCELL_OS=centos ./pgbackup-dev manifest warden
```

Edit the manifest file you downloaded (`~/workspace/manifests/pgbackup.yml`) and add settings as follows.

Add to the list of known `releases: `

```yaml
releases:
#...
- name: pgbackup
  version: latest
```

For consistency also add to the `releases: ` section under `meta: `

```yaml
meta:
  environment: pgbackup-development
  releases:
  - name: cf
    version: latest
  - name: pgbackup
    version: latest
```

Add properties such as tags.

```yaml
properties:
# ... lots of properties ... at bottom put vv
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

Note that for each job you create in your release that you want to run on a
Job VM you must add a `templates:` entry with the `name:` of the template
and the `release:` from which it comes.

## Deployments Blobs

The script `./pgbackup-dev blobs` is used to prepare the `blobs/` directory
runs each package's `prepare` script from within the `blobs/`
directory. This will run each package's `prepare` script, if it exists,
which *should* download and prepare that package's required blobs
(source tarballs, etc...) into the {package}/ directory.

## Development

Download your manifest file for the deployment targeted.

Target your BOSH Director as explained above, if you already have a deploy be sure to also download your manifest as per above.

If this is your first time cloning the release repository for develompent first prepare all of the things:
```sh
./pgbackup-dev prepare warden
```

This is equivalent to:
```sh
./pgbackup-dev blobs
./pgbackup-dev release
./pgbackup-dev stemcell warden
./pgbackup-dev manifest warden
```

Once these steps have all been completed

```sh
bosh -n deploy
```

## Debugging & QA

See `docs/notes.md` for more information.

In order to gain access to one of the VMs, from a terminal `bosh ssh pgbackup {VM Index}`,
for example to ssh to the first node:
```sh
bosh ssh pgbackup 0 # Hop on and have a look around...
```

If you want to destroy and recreate on specific node, say `pgbackup/0`, you do the following:

```sh
bosh -n recreate pgbackup 0 --force
```

See `docs/pgbackup.md` for release specific information.

