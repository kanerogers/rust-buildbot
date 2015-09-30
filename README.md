# The Rust Project's buildbot config

This is the code for the buildbot instance used by Rust at
http://buildbot.rust-lang.org/builders. It is currently not in a
condition that allows people to easily set up their own instances.

# Slave configuration

Slaves communicate with buildbot through an ssh tunnel for which
you'll need the stunnel tool. Use whichever version of of the
buildslave software the other slaves are running.

This repo includes a configure script for creating the stunnel
configuration. From within the repo, run 'setup-slave.sh', enter the
name, password, and master address that you were provided. This
creates the stunnel configuration file called
`rust-buildbot-slave-stunnel-final.conf`.

The first time you run this script it will start stunnel and the
buildslave, but typically you would run stunnel and buildslave at
reboot using cron:

```
> stunnel rust-buildbot-slave-stunnel-final.conf && buildslave restart slave
```

# Running the Master

```
$ cd rust-buildbot/master
$ buildbot start
```

# Adding a new builder

This requires some digging in `master.cfg`. If the new builder will be gated,
you'll also have to add it in `/home/rustbuild/homu/cfg.toml`.

* Choose the new builder's name. 

    * If it runs tests with optimizations enabled, the name will contain
      `-opt`. 

    * If the tests are run without optimizations, the name should contain
      `-nopt-t`. 

    * If you're toggling a new option, pick the unique string that represents
      the option at hand.

* Add the new builder name to the `auto_platforms_dev` and
  `auto_platforms_prod` lists.

* Add the new builder to `dist_nogate_platforms` (or the alternative for
  gated). Its name will have acquired an `auto-` prefix at this point.

* Under `for p in auto_platforms`, add logic to check for the unique string
  from the name. Yes, it's terrible. I'm sorry.

* Under the next `for p in auto_platforms`, set your new flag according to the
  value you read from the unique string in the previous step.

Pull requests to simplify this workflow are welcome. 

# License

This software is distributed under the terms of both the MIT license
and/or the Apache License (Version 2.0), at your option.

See [LICENSE-APACHE](LICENSE-APACHE), [LICENSE-MIT](LICENSE-MIT) for details.
