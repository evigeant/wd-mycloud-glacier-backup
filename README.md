# wd-mycloud-glacier-backup
Regular backups of WD MyCloud to AWS Glacier

## Pre-requisites
This is mostly for personal purposes and assumes that the following are already made
- You own a WD MyCloud Mirror device
- You have an AWS account
- You have enabled SSH access to you WD MyCloud Device

## Instructions
### Get a working python environment with AWS boto3
I wanted to use boto3 the AWS SDK for python on my WD MyCloud Mirror device, but unfortunately, it was not so simple. The python installed is read-only, so I started by creating a [VirtualEnv](https://packaging.python.org/key_projects/#virtualenv).
```sh
# Go somewhere there is a lot of space on the device
cd /mnt/HD/HD_a2
mkdir python
cd python
# Download VirtualEnv
curl -LO https://github.com/pypa/virtualenv/archive/16.0.0.tar.gz
# Unpack the archive
tar -xvzf 16.0.0.tar.gz
# Go back to root of RAID volume
cd ..
# Create the virtual environment
python virtualenv-16.0.0/virtualenv.py py-env
# Source it into the current shell
source py-env/bin/activate
```
### Download boto3
It turns out that the native python does not include SSL support and all repositories are now SSL... So I downloaded all dependencies
```sh
cd py-env
mkdir archives
cd archives
curl -LO https://files.pythonhosted.org/packages/b6/e7/e13540cf09a8263a7e40c24f54a3552ec00dd519e7d8eb24d40f0d
d18a3c/boto3-1.7.62.tar.gz
curl -LO https://files.pythonhosted.org/packages/90/ef/c6618be9c5e9278af1d82690b044561be83954fb62ecdbe4433910
aaf224/botocore-1.10.62.tar.gz
curl -LO https://files.pythonhosted.org/packages/e5/21/795b7549397735e911b032f255cff5fb0de58f96da794274660bca
4f58ef/jmespath-0.9.3.tar.gz
curl -LO https://files.pythonhosted.org/packages/9a/66/c6a5ae4dbbaf253bd662921b805e4972451a6d214d0dc9fb3300cb
642320/s3transfer-0.1.13.tar.gz
curl -LO https://files.pythonhosted.org/packages/84/f4/5771e41fdf52aabebbadecc9381d11dea0fa34e4759b4071244fa0
94804c/docutils-0.14.tar.gz
curl -LO https://files.pythonhosted.org/packages/a0/b0/a4e3241d2dee665fea11baec21389aec6886655cd4db7647ddf96c
3fad15/python-dateutil-2.7.3.tar.gz
curl -LO https://files.pythonhosted.org/packages/e5/62/f9e1ac314464eb5945c97542acb6bf6f3381dfa5d7a658de7730
c36f31a1/setuptools_scm-2.1.0.tar.gz
```

### Install boto3
For the install to work, we need to point the tmp directory to a place which has enough space because /tmp is in a very small tmpfs
```sh
mkdir /mnt/HD/HD_a2/tmp
export TMPDIR=/mnt/HD/HD_a2/tmp
pip -vvv install --no-index --find-links=. --find-links=../../virtualenv-16.0.0/virtualenv_support/ boto3
```
