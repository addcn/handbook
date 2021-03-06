
## 概念简介

**OpenStack**：OpenStack是一个由NASA和Rackspace合作研发并发起的，以Apache许可证授权的自由软件和开放源代码项目。项目目标是提供实施简单、可大规模扩展、丰富、标准统一的云计算管理平台。OpenStack通过各种互补的服务提供了基础设施即服务（IaaS）的解决方案，每个服务提供API以进行集成。OpenStack是用Python编程语言编写的。

**DevStack**：可以自动安装OpenStack的工具。

**OpenStack核心项目**：OpenStack是由很多核心项目组合在一起的。

**计算(Compute)**：Nova。一套控制器，用于为单个用户或使用群组管理虚拟机实例的整个生命周期，根据用户需求来提供虚拟服务。负责虚拟机创建、开机、关机、挂起、暂停、调整、迁移、重启、销毁等操作，配置CPU、内存等信息规格。自Austin版本集成到项目中。

**对象存储(Object Storage)**：Swift。一套用于在大规模可扩展系统中通过内置冗余及高容错机制实现对象存储的系统，允许进行存储或者检索文件。可为Glance提供镜像存储，为Cinder提供卷备份服务。自Austin版本集成到项目中。

**镜像服务(Image Service)**：Glance。一套虚拟机镜像查找及检索系统，支持多种虚拟机镜像格式(AKI、AMI、ARI、ISO、QCOW2、Raw、VDI、VHD、VMDK)，有创建上传镜像、删除镜像、编辑镜像基本信息的功能。自Bexar版本集成到项目中。

**身份服务(Identity Service)**：Keystone。为OpenStack其他服务提供身份验证、服务规则和服务令牌的功能，管理Domains、Projects、Users、Groups、Roles。自Essex版本集成到项目中。

**网络&地址管理(Network）**：Neutron。提供云计算的网络虚拟化技术，为OpenStack其他服务提供网络连接服务。为用户提供接口，可以定义Network、Subnet、Router，配置DHCP、DNS、负载均衡、L3服务，网络支持GRE、VLAN。插件架构支持许多主流的网络厂家和技术，如OpenvSwitch。自Folsom版本集成到项目中。

**块存储(Block Storage)**：Cinder。为运行实例提供稳定的数据块存储服务，它的插件驱动架构有利于块设备的创建和管理，如创建卷、删除卷，在实例上挂载和卸载卷。自Folsom版本集成到项目中。

**UI 界面(Dashboard)**：Horizon。OpenStack中各种服务的Web管理门户，用于简化用户对服务的操作，例如：启动实例、分配IP地址、配置访问控制等。自Essex版本集成到项目中。

**pip**：是一个Python包管理工具，主要是用于安装 PyPI 上的软件包，DevStack安装OpenStack的时候会使用到这个工具。

## 安装

```bash
wget --no-check-certificate http://rdo.fedorapeople.org/rdo-release.rpm
rpm -ivh rdo-release.rpm

yum install -y openstack-packstack
yum -y update

# 接下来一键安装OpenStack，--install-hosts参数需要输入你的IP地址
packstack --install-hosts=your_ip; # 提示输入的时候输入你的密码，等待安装完成即可。
packstack --allinone

# 输出结果
You will find full trace in log /var/tmp/packstack/20170113-125411-kkk_H3/manifests/192.168.188.127_controller.pp.log
Please check log file /var/tmp/packstack/20170113-125411-kkk_H3/openstack-setup.log for more information
Additional information:
 * A new answerfile was created in: /root/packstack-answers-20170113-125412.txt
 * Time synchronization installation was skipped. Please note that unsynchronized time on server instances might be problem for some OpenStack components.
 * File /root/keystonerc_admin has been created on OpenStack client host 192.168.188.127. To use the command line tools you need to source the file.
 * To access the OpenStack Dashboard browse to http://192.168.188.127/dashboard .
Please, find your login credentials stored in the keystonerc_admin in your home directory.
 * To use Nagios, browse to http://192.168.188.127/nagios username: nagiosadmin, password: de7af5954a834f89

# reboot 重启机器
```

## 登陆Dashboard

```bash
cat /root/keystonerc_admin 

export OS_USERNAME=admin
export OS_TENANT_NAME=admin
export OS_PASSWORD=d6433394e15f4175
export OS_AUTH_URL=http://10.1.199.8:35357/v2.0/
export PS1='[\u@\h \W(keystone_admin)]\$ '
```

## 错误处理

### devstack部署openstack遇到rdo源配置问题及解决办法

```bash
yum install http://rdo.fedorapeople.org/rdo-release.rpm
无法打开 http://rdo.fedorapeople.org/rdo-release.rpm ，跳过。
错误：无须任何处理
```

解决办法

```bash
wget --no-check-certificate http://rdo.fedorapeople.org/rdo-release.rpm
rpm -ivh rdo-release.rpm
```

### 端口被占用

```shell
# 查看到80端口
netstat -lnp|grep 80

tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      27906/nginx: master
tcp        0      0 0.0.0.0:6080            0.0.0.0:*               LISTEN      32437/python2
unix  2      [ ACC ]     STREAM     LISTENING     21680    2003/master          public/flush
unix  2      [ ACC ]     STREAM     LISTENING     13380    1/systemd            /run/lvm/lvmetad.sock
```

杀掉或者停用服务

```shell
kill -9 27906
```


```
warning: /var/cache/yum/x86_64/7/openstack-newton/packages/hiera-1.3.4-5.el7.noarch.rpm: Header V4 RSA/SHA1 Signature, key ID 764429e6: NOKEY2.4 MB  00:00:53 ETA
hiera-1.3.4-5.el7.noarch.rpm 的公钥尚未安装
```


```
ERROR : Error appeared during Puppet run: 192.168.188.127_controller.pp
Error: Could not prefetch nova_flavor provider 'openstack': Command: 'openstack ["flavor", "list", "--quiet", "--format", "csv", ["--long", "--all"]]' has been running for more than 40 seconds (tried 4, for a total of 170 seconds)
```

## 参考知识

- [在 CentOS7.2 上安装 OpenStack Liberty 版](http://www.infocool.net/kb/OpenStack/201609/187078.htmldddAA)
- [OpenStack部署都有哪些方式](http://www.trystack.cn/Articles/openstack-deployment.html)
- [Openstack安装部署](http://promisejohn.github.io/2015/05/07/HelloOpenstack/)
- [CentOS 6.4 RDO测试](http://www.chenshake.com/centos-6-4-rdo-test/)
- [Install And Configure Openstack Mitaka RDO On CentOS 7](http://linuxpitstop.com/openstack-mitaka-rdo-on-centos-7/)
- [Openstack Havana Dashboard测试和使用](http://www.chenshake.com/openstack-havana-dashboard-to-test-and-use/#i)