# Setup kolla-ansible

Adapted from [OpenStack using Kolla Ansible guide](https://docs.openstack.org/kolla-ansible/xena/user/quickstart.html).


## 1. Install requirements

Configure Python environment:

```shell
pip install -U pip
pip install -r requirements.txt
ansible-galaxy install -f -r requirements.yml -p roles/
```

For Mac, install coreutils (see [matejak/argbash#74](https://github.com/matejak/argbash/issues/74)):

```shell
brew install coreutils
echo 'export PATH="/opt/homebrew/opt/coreutils/libexec/gnubin:$PATH"' >> ~/.zshrc
```


2. Configure node

Setup users and security:

```shell
# supply sudo password on first run after Ubuntu install
ansible-playbook -i ./all-in-one deploy-node.yml --ask-become-pass --tags security
# then
ansible-playbook -i ./all-in-one deploy-node.yml --tags security
```

Next, configure LVM and partitions for Cinder and Swift. I found [A Linux user's guide to Logical Volume Management](https://opensource.com/business/16/9/linux-users-guide-lvm) a helpful resource.

```shell
ansible-playbook -i ./all-in-one deploy-node.yml --tags cinder
ansible-playbook -i ./all-in-one deploy-node.yml --tags swift
```


## 2. Setup

Start with Kolla boilerplate:

```shell
export CONFIG=$PWD/altair/
mkdir $CONFIG
cp -r $VIRTUAL_ENV/share/kolla-ansible/etc_examples/kolla/* $CONFIG
cp $VIRTUAL_ENV/share/kolla-ansible/ansible/inventory/* .
```

Edit `./all-in-one` to point to your OpenStack node.

Run random password generator:

```shell
kolla-genpwd --passwords $CONFIG/passwords.yml
```

Edit `$CONFIG/globals.yml` as needed.


## 3. Deployment

1. Bootstrap servers with kolla deploy dependencies:

```shell
kolla-ansible --configdir=$CONFIG -i ./all-in-one bootstrap-servers
```

2. Do pre-deployment checks for hosts:

```shell
kolla-ansible --configdir=$CONFIG -i ./all-in-one prechecks
```

3. Finally proceed to actual OpenStack deployment:

```shell
kolla-ansible --configdir=$CONFIG -i ./all-in-one deploy
```

When this playbook finishes, OpenStack should be up, running and functional.
