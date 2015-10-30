# BOSH Release Notes

## BOSH Lite

In order to get your bosh-lite up and running, first install Vagrant and Virtualbox
and then do:

```sh
git clone git://github.com/cloudfoundry/bosh-lite
cd bosh-lite
vagrant up
./bin/add-route
```

If you wish to destroy BOSH Lite:

```sh
vagrant destroy --force
```

## Debugging on a VM

For convenience when you ssh to a VM run the following line in order to add all
of the package binaries to your current path

```sh
for _path in $(ls -d /var/vcap/packages/*/*bin) ; do PATH="${_path}:${PATH}" ; done ; export PATH
```
(eg. copy and paste it into the terminal window)

In order to examine the status of your BOSH services you can run:
```sh
monit summary
```

If you want to restart a service process, say consul, you would do it as follows:
```sh
monit restart consul
```

Then you can watch the status as it restarts with the following command:
```sh
watch -n 2 monit summary
```

Which will run the `monit summary` command every 2 seconds with it's output as
the only thing on the screen. Press `^C` (control+C) in order to exit when you
are done.

To inspect the pacages that BOSH has placed on the box:

```sh
ls /var/vcap/packages
```

To inspect the jobs that BOSH has placed on the box:
```sh
ls /var/vcap/jobs
```

Logs for BOSH jobs *should* all be located within:
```sh
ls /var/vcap/sys/log/*/*.log
```


Any persistant data storage for a job will be located within:
```sh
ls /var/vcap/store/*/
```

