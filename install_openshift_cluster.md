# 在 AWS 安装多节点 Openshift

<https://github.com/adu-21/openshift-ansible-contrib/tree/master/reference-architecture/aws-ansible>

## 准备

- 使用 Route53 申请一个  registered Domain
- 选择一个至少有 3 个 AZ 的 Region

## 开始

### 创建堡垒机

从marketplace 找一个centos7的ami创建一个 EC2 instance 并绑定一个 EIP，开启 22 端口

登录到 bastion 上，

```
yum install subscription-manager -y 

yum -y install atomic-openshift-utils ansible openshift-ansible-playbooks
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum -y install python2-boto \
                 pyOpenSSL \
                 git \
                 python-netaddr \
                 python-six \
                 python2-boto3 \
                 python-click \
                 python-httplib2
                 
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum -y install python-pip git python2-boto \
                 python-netaddr python-httplib2 python-devel \
                 gcc libffi-devel openssl-devel python2-boto3 \
                 python-click python-six pyOpenSSL
mkdir -p /usr/share/ansible/openshift-ansible
git clone https://github.com/openshift/openshift-ansible.git /usr/share/ansible/openshift-ansible

vi ~/.ssh/config
Host *.oc-tw.net 
     ProxyCommand               ssh ec2-user@bastion -W %h:%p
     IdentityFile               /home/root/.ssh/oc_yidong

Host bastion
     Hostname                   bastion.oc-tw.net
     user                       ec2-user
     StrictHostKeyChecking      no
     ProxyCommand               none
     CheckHostIP                no
     ForwardAgent               yes
     IdentityFile               /path/to/ssh/key
     
     
     
export AWS_ACCESS_KEY_ID=foo
export AWS_SECRET_ACCESS_KEY=bar

```

### 设置 Github OAuth

最后一步

```
./ose-on-aws.py --keypair=yidong_sydney --public-hosted-zone=oc-tw.net --deployment-type=origin --ami=ami-fedafc9d \
--github-client-secret=4189c40dfa0a311881fcd808f0cfeb51bc9e6939 
--github-organization=openshift \
--github-organization=ThoughtWorks-Chengdu-DevOps-Club --github-client-id=84dd0bf53dbfc4759baa --region ap-southeast-2
```

```
sudo sed -i.bak -e '/Defaults.*requiretty/s/^/#/' /etc/sudoers
```

sudo mkdir -p  /etc/rhsm/ca && sudo touch /etc/rhsm/ca/redhat-uep.pem



rpm -Uvh ftp://rpmfind.net/linux/centos/7.4.1708/extras/x86_64/Packages/python-passlib-1.6.5-2.el7.noarch.rpm