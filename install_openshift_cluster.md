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
     IdentityFile               /root/.ssh/yidong_ohio_openshift.pem

Host bastion
     Hostname                   bastion.oc-tw.net
     user                       ec2-user
     StrictHostKeyChecking      no
     ProxyCommand               none
     CheckHostIP                no
     ForwardAgent               yes
     IdentityFile               /root/.ssh/yidong_ohio_openshift.pem
     
     
     
export AWS_ACCESS_KEY_ID=foo
export AWS_SECRET_ACCESS_KEY=bar

```

### 设置 Github OAuth

```
git clone https://github.com/ADU-21/openshift-ansible-contrib.git
```

安装Ansible 监控

```
pip install ara
export ANSIBLE_CALLBACK_PLUGINS="$(python -c 'import os,ara; print(os.path.dirname(ara.__file__))')/plugins/callbacks"
ara-manage runserver -h 0.0.0.0 &
```

```
pip install --upgrade pip && pip uninstall Pygments && pip install Pygments
```





坑

```
sudo yum-config-manager --enable rhui-REGION-rhel-server-extras
sudo yum install -y httpd-tools
rpm -Uvh ftp://rpmfind.net/linux/centos/7.4.1708/extras/x86_64/Packages/python-passlib-1.6.5-2.el7.noarch.rpm
```

最后一步

```
./ose-on-aws.py \
--keypair=yidong_ohio_openshift \
--public-hosted-zone=oc-tw.net \
--deployment-type=origin \
--ami=ami-cfdafaaa \
--github-client-secret=4189c40dfa0a311881fcd808f0cfeb51bc9e6939 \
--github-organization=ThoughtWorks-Chengdu-DevOps-Club \
--github-client-id=84dd0bf53dbfc4759baa \
--region=us-east-2
```

安装Docker:

```
all_hosts="master01 master02 master03 app-node01 app-node02 infra-node01 infra-node02 infra-node03"
for h in $all_hosts; do ssh ec2-user@ose-$h.oc-tw.net 'sudo yum-config-manager --enable rhui-REGION-rhel-server-extras -y'; done
```



https://openshift-master.oc-tw.net/console/

https://registry-console-default.apps.oc-tw.net/



https://openshift-master.oc-tw.net/console/command-line

oc login https://openshift-master.oc-tw.net --token=bcD3sprC9hjOgopov9VfbHEaBbPK03WQt23MXm-KtGw

oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-ex.git













guanging:

```
./ose-on-aws.py --region=us-east-2 --keypair=lgm-oc --public-hosted-zone=oc-tw.net --deployment-type=origin --ami=ami-cfdafaaa \
--github-client-secret=6497d7521551c5e98a834c47b1f073e255fafb81 --github-organization=ThoughtWorksInc \
--github-client-id=0997d0f5c18fd096d5f5

./add-node.py --region=us-east-2 --keypair=lgm-oc --public-hosted-zone=oc-tw.net --deployment-type=origin --ami=ami-cfdafaaa \
--use-cloudformation-facts --subnet-id=subnet-1139825c \
--node-type=app --shortname=ose-app-node03 --existing-stack=openshift-infra

ansible-playbook -i inventory/aws/hosts \
    -e 'region=us-east-2 stack_name=openshift-infra ci=true' \
    -e 'extra_app_nodes=openshift-infra-ose-app-node03' \
    playbooks/teardown.yaml
```

