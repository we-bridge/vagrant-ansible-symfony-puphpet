## Usage

You should already have [Vagrant](http://vagrantup.com/) installed.

Then edit the Vagrantfile to uncomment the application name part, to put your project very own name.
After that simply run

```bash
vagrant up
```

Note: if you already have a service using the port 8080, or if you have already use this repository to create an other symfony2 project, you need to the Vagrantfile to chose an other port than 8080, then run `vagrant up`

The virtual machine will be automatically prepared ("provisioned", through [Ansible](http://docs.ansible.com/) "playbooks") in accordance with the "roles" defined in the `provisioning` directory:

0. "apache", "php", "git", etc. installs and configures these system services.
0. "postgresql" OR "mysql" (depending on the `DBTYPE` variable declared in the `Vagrantfile`) creates the database server and credentials
0. "symfony" downloads the basic tools required to work with Symfony (-its installer) and PHP (-Composer, for dependency management).
0. If APPNAME is set in the Vagrantfile, then "webapp" initializes a shiny new application in an eponymous directory. Also if APPNAME is set, "webapp-container" prepares the frontend web server (-a VirtualHost entry) and backend processes (-a FastCGI process group) for your application.
0. Finally, "vagrantbox" just applies a few additional system-level optimizations to the virtual server.

You can then go in your virtual machine using `vagrant ssh` and your project's files will be accessible in `/vagrant/your_application_name`. From there you can run composer to install any new additional dependencies you will add, or run the `php app/console ....` of symfony2.

To open the website itself, you simply need to open `localhost:8080` (if you changed the port, of course replace 8080) in your browser.

## Starting off a new project

Using this template for starting a new Symfony webapp project from scratch, a typical workflow could resemble something like the following:
 1. Initialize a new empty git repo for your project, let's say it is at `http://gitlab.local/projects/my-app-repo.git/
 2. Clone it into a working copy, thereby setting it's `origin` remote: `cd ~/Projects/; git clone http://gitlab.local/projects/my-app-repo.git/; cd my-app-repo/`
 3. Merge a remote branch originating from this template repo, e.g. `git remote add -t master gh-base https://github.com/we-bridge/vagrant-ansible-symfony.git ; git fetch gh-base; git merge gh-base/master` _or_ simply `git pull https://github.com/we-bridge/vagrant-ansible-symfony.git`
 4. (optional) If the previous commit log bothers you, replace it all with a single commit before doing anything else: `git reset $(git commit-tree HEAD^{tree} -m "Initialized from template")`

### Checking out existing projects
Usually `composer install` is run automatically during provisioning. However, if for some reason (network problems? Github API limits?) this step fails, your project will typically generate errors related to `PHP Fatal error:  require_once(): Failed opening required '(...)/app/bootstrap.php.cache'` etc, since the cache bootstrap file is built during composer's post-install step. If this happens, simply re-run either `vagrant provision` or `composer install` manually once to fix up these issues.

## Requirements

  * ansible > 1.6

Note: on ubuntu 14.04 you have to install ansible with `pip` rather than `apt-get`
or if you don't want to use `pip` to install system-wide commands, you can install ansible PPA

```
apt-get install python-software-properties
add-apt-repository ppa:ansible/ansible
apt-get update
apt-get install ansible
```

## Customization

If you want to replace apache2, by say Nginx, you can still use this repository and simply add an additional role to the provisioning. Pull Request / Patches are welcome.

