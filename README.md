Vagrant LAMP Stack
============

A MAMP replacement that runs Apache, MySQL and PHP under an Ubuntu environment.

Why Vagrant over MAMP?
----------------------

MAMP turns your Mac into a server. But what happens when you install the latest OS X? Will MAMP still function properly? Instead of running your computer as a server, Vagrant and VirtualBox set up a server environment in the form of a virtual machine. This means several good things:

* Full control of the server. Install any software you like without breaking your Mac.
* Easy to set up. Installing this environment is as simple as running two shell commands: `$ git clone git@github.com:aaronbushnell/vagrant-lamp.git` and `$ vagrant up`. Vagrant sets the server up exactly how you need it.
* Easy to tear down. Mess your server up? Just run `$ vagrant destroy` and reinstall. You'll have a brand new environment ready to go.
* MailCatcher. Any emails sent in the Vagrant environment are caught by MailCatcher and can be viewed by going to http://local.dev:1080. No more refreshing your inbox and checking your spam for hours on end.

Caveats
-------
* I couldn't run Parallels and VirtualBox simultaneously so you may need to run your IE virtual machines in VirtualBox instead of Parallels. Seems to work just fine and it is free so I don't mind this.

What's inside
-------------

Installed software:

* Apache
* MySQL
* php
* phpMyAdmin
* Xdebug with Webgrind
* zsh with [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
* git, subversion
* mc, vim, screen, tmux, curl
* [MailCatcher](http://mailcatcher.me/)
* [Composer](http://getcomposer.org/)
* Phing

How-to
------

### Install

1. Download and install [VirtualBox](http://www.virtualbox.org/)
2. Download and install [vagrant](http://vagrantup.com/)
3. Clone this repository `$ git clone git@github.com:aaronbushnell/vagrant-lamp.git`
4. Launch the box `$ vagrant up`
5. Point to the server on your Mac by opening `/etc/hosts` in your code editor of choice and add `192.168.33.10 local.dev`

### Add a new project

* Clone a project into the `public` directory
* Add a new [project-name].json file in `data_bags/sites` an example .json file

```json
{
  "id": "my-site",
  "host": "local.my-site.com",
  "docroot": "/vagrant/public/my-site/public_html",
  "include": [
    "/vagrant/public/my-site/my-site.conf"
  ],
  "aliases": [
    "my-site.*.xip.io"
  ]
}
```

* Run `$ vagrant halt && vagrant up && vagrant provision`
* Point to the project on your Mac by opening `/etc/hosts` in your code editor of choice and add `192.168.33.10 local.my-site.com`
* Setup any databases needed via [phpMyAdmin](http://local.dev/phpmyadmin)

Notes
-----

### MySQL

The guests local 3306 port is available on the host at port 33066.

### phpMyAdmin

    http://local.dev/phpmyadmin
    Username: root
    Password: vagrant

### XDebug and webgrind

XDebug is configured to connect back to your host machine on port 9000 when
starting a debug session from a browser running on your host. A debug session is
started by appending GET variable XDEBUG_SESSION_START to the URL (if you use an
integrated debugger like Eclipse PDT, it will do this for you).

XDebug is also configured to generate cachegrind profile output on demand by
adding GET variable XDEBUG_PROFILE to your URL. For example:

    http://local.dev/index.php?XDEBUG_PROFILE

Webgrind is available on each domain. For example:

    http://local.dev/webgrind

It looks for cachegrind files in the `/tmp` directory, where xdebug leaves them.

**Note:** xdebug uses the default value for xdebug.profiler_output_name, which
means the output filename only includes the process ID as a unique part. This
was done to prevent a real need to clean out cachgrind files. If you wish to
configure xdebug to always generate profiler output
(`xdebug.profiler_enable = 1`), you *will* need to change this setting to
something like

    xdebug.profiler_output_name = cachegrind.out.%t.%p

so your call to webgrind will not overwrite the file for the process that
happens to serve webgrind.

### MailCatcher

All emails sent by PHP are intercepted by MailCatcher. So normally no email would be delivered outside of the virtual machine. Instead you can check messages using web frontend for MailCatcher, which is running on port 1080 and also available on every domain:

    http://local.dev:1080

### Composer

Composer binary is installed globally (to `/usr/local/bin`), so you can simply call `composer` from any directory.
