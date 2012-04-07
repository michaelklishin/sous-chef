# What is Travis Sous Chef

Travis Sous Chef is a repository that makes development and testing of [Chef](http://www.opscode.com/chef/) cookbooks (for [Travis CI](http://travis-ci.org) but
not just) easy. It brings practices that the [Travis Development Team](https://github.com/travis-ci) uses to develop
new and test modified [cookbooks](https://github.com/travis-ci/travis-cookbooks/tree/master/ci_environment) for
[Travis CI](http://travis-ci.org) to the masses.

Sous Chef intentionally focuses on automating away as many distractions as possible so you can focus on developing your Chef cookbooks. It does just one thing but does it well.


## How it works

With Sous Chef, you use Vagrant and a locally running VirtualBox VM to develop and test your cookbooks. Sous Chef can use any collection of cookbooks: [those we use for travis-ci.org](https://github.com/travis-ci/travis-cookbooks/tree/master/ci_environment), cookbooks you are using for
commercial projects, cookbooks from the [official OpsCode repository](http://github.com/opscode/cookbooks) or anything else.

With Sous Chef, you provision a locally running virtual machine managed by [Vagrant](http://vagrantup.com) in just one command. The process is
set up to shorten the feedback loop:

 * Modify your cookbook recipes (or templates/files in them)
 * Run provisioning
 * See and verify the result
 * Rinse and repeat

Once you are done with your cookbooks, just push them to a source control repository or rsync them to your server. Then destroy VM environment
you were using in one command. Or just power off the VM and come back to work with it later.


## Dependencies

Sous Chef uses [Vagrant](http://vagrantup.com) and thus relies on [Virtual Box](http://virtualbox.org) being installed. Note that you do not
need to have Chef installed locally. It will only be run in a virtual machine.


## Getting started with Sous Chef

First install [VirtualBox 4.1.x](https://www.virtualbox.org/wiki/Downloads):

* For [Mac OS X](http://download.virtualbox.org/virtualbox/4.1.12/VirtualBox-4.1.12-77245-OSX.dmg)
* For [64-bit Linux distributions](http://download.virtualbox.org/virtualbox/4.1.12/)

Then install the vagrant gem:

    gem install vagrant --version ">= 1.0"

Copy sample Vagrant file:

    cp Vagrantfile.sample Vagrantfile

Create a 32-bit Ubuntu virtual machine you will be developing cookbooks in:

    vagrant up 

Once VM is created, create cookbooks directory or clone an existing collection of cookbooks like [Travis CI cookbooks](https://github.com/travis-ci/travis-cookbooks):

    mkdir cookbooks
    # or
    git clone git://github.com/travis-ci/travis-cookbooks.git cookbooks

To allow provisioning with chef you need to uncomment the following line
(including its associated end statement)

      config.vm.provision :chef_solo do |chef|


After that point Vagrant at the cookbooks location by editing Vagrantfile. For Travis CI cookbooks, you just need to uncomment

    # you can use multiple cookbook locations if necessary.
    # For example, to develop both shared OSS cookbooks and your private
    # product/company-specific ones.
    # chef.cookbooks_path = ["cookbooks/ci_environment"]

for other cookbook collections, provide a local path (or several) like so

    chef.cookbooks_path = ["cookbooks"]

Next choose some cookbooks to provision. In the case of Travis CI cookbooks, build-essential is a good one to start with, so uncomment

    # chef.add_recipe     "build-essential" 

Your Vagrantfile then will look like this:

    Vagrant::Config.run do |config|
      config.vm.box     = "oneiric32_base"
      config.vm.box_url = "http://files.travis-ci.org/boxes/bases/oneiric32_base.box"
    
      config.vm.provision :chef_solo do |chef|
        # point Vagrant at the location of cookbooks you are going to use,
        # for example, a clone of your fork of github.com/travis-ci/travis-cookbooks
         chef.cookbooks_path = ["cookbooks/ci_environment"]
    
        # Turn on verbose Chef logging if necessary
        # chef.log_level      = :debug
    
        # List the recipies you are going to work on/need.
        chef.add_recipe     "build-essential"    
        # chef.add_recipe     "networking_basic"    
        # chef.add_recipe     "kerl"
    
        # chef.json.merge!({
        #   :kerl => {
        #     :user     => "vagrant",
        #     :group    => "vagrant"
        #   }
        # })
      end
    end

Then provision your VM (this will run chef-solo and converge VM to the state you want using Chef recipes you chosen to use):

    vagrant provision


Running chef-solo may take from several seconds to several minutes, dependeing on what selected recipes do. Building 13 Ruby versions/implementations
for travis-ci.org, for example, takes over 50 minutes but most of recipes run in under 30 seconds.

Once provisioning finishes, ssh into the VM to check what the environment looks like:

    vagrant ssh

When you are done with your work on the cookbook you can either power off the VM to use it later with

    vagrant halt

or destroy it completely with

    vagrant destroy


## Developing a new cookbook with Sous Chef

TBD


## Copyright

Michael S. Klishin & Travis CI Development Team, 2011-2012


## License

The MIT license. See LICENSE file in the repository root.
