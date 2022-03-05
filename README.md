# Setup kolla-ansible

Adapted from [OpenStack using Kolla Ansible guide](https://docs.openstack.org/kolla-ansible/xena/user/quickstart.html).


## 1. Install requirements

Configure Python environment:

```shell
pip install -U pip
pip install -r requirements.txt
```

For Mac, install coreutils (see [matejak/argbash#74](https://github.com/matejak/argbash/issues/74)):

```shell
brew install coreutils
echo 'export PATH="/opt/homebrew/opt/coreutils/libexec/gnubin:$PATH"' >> ~/.zshrc
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

```shell
kolla-ansible --configdir=$CONFIG -i ./all-in-one bootstrap-servers
```
