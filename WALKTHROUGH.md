# Walkthrough

This walkthrough assumes you're using a Bash shell.  Hopefully all of the Vagrant & VirtualBox commands will work as written.  You may need to adjust some of the commands to work in any other shell (e.g. use the Windows equivalent for `mkdir -p ./host/tmp`).

# Initialize a Vagrant box on host

As of 2018-02-06, it looks like the Edge version of Docker CE work on Unbuntu 17.10 (Artful Aardvark), so let's create a initialize a Vagrant box using that.

    $ vagrant init ubuntu/artful64
    $ vagrant up

# Set up a synced folder between host & guest

At some point, you may want to sync files between the host machine and the guest VM.  To do this, install the VirtualBox Guest Additions.

    $ vagrant ssh -c /vagrant/host/bin/ubuntu_artful64_install_virtualbox_guest_additions

Now add config setting to the Vagrantfile.

    Vagrant.configure("2") do |config|
      config.vm.synced_folder "./host", "/host"
    end

Then restart the VM.

    $ vagrant reload

If everything is working, `./host` on the host & `/host` on the guest should sync when files are updated.

    on host:  create tmp directory
    $ mkdir -p ./host/tmp

    # on host:  shared directory is empty
    $ ls ./host/tmp
    $

    # on guest:  shared directory is empty
    $ $ vagrant ssh -c 'ls /host/tmp'
    Connection to 127.0.0.1 closed.
    $

    # on host:   create a testfoo file
    # on guest:  testfoo is present
    $ touch ./host/tmp/testfoo
    $ vagrant ssh -c 'ls /host/tmp'
    testfoo
    Connection to 127.0.0.1 closed.
    $

    # on guest:  create a testbar file
    # on host:   testbar is present
    $ vagrant ssh -c 'touch /host/tmp/testbar'
    Connection to 127.0.0.1 closed.
    $ ls ./host/tmp
    testbar	testfoo
    $
