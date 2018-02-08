# Walkthrough

This walkthrough assumes you're using a Bash shell.  Hopefully all of the Vagrant & VirtualBox commands will work as written.  You may need to adjust some of the commands to work in any other shell (e.g. use the Windows equivalent for `mkdir -p ./host/tmp`).

# Initialize a Vagrant box on host

As of 2018-02-06, it looks like the Edge version of Docker CE work on Unbuntu 17.10 (Artful Aardvark), so let's create a initialize a Vagrant box using that.

    $ vagrant init ubuntu/artful64
    $ vagrant up

# Set up a synced folder between host & guest

At some point, you may want to sync files between the host machine and the guest VM.  To do this, install the VirtualBox Guest Additions.

    $ vagrant ssh -c /vagrant/host/bin/ubuntu_artful64_install_virtualbox_guest_additions
    VirtualBox Guest Additions vX.X.X installed (it probably worked!)
    $

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

# Install Docker CE (Edge channel) on guest

Before we can start running containers, we'll need to set up Docker CE.  Let's do that now!

    $ vagrant ssh -c '/vagrant/host/bin/ubuntu_artful64_install_docker_ce 17.12.0~ce-0~ubuntu'
    Docker CE vX.X.X install complete (it probably worked!)
    $

That's it!  Docker should now be be installed & configured to start at boot.  To test, reload the VM and look for the Docker daemon in the process list.

    $ vagrant reload
    $ vagrant ssh -c 'ps -ef | grep docker'
    $ vagrant ssh -c 'ps -ef | grep docker'
    root       994     1  0 23:21 ?        00:00:00 /usr/bin/dockerd -H fd://
    root      1066   994  0 23:21 ?        00:00:00 docker-containerd --config /var/run/docker/containerd/containerd.toml
