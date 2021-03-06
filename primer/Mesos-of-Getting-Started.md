<title>Mesos 入门介绍：包括 Mesos 的下载、安装等注意事项</title>
<description>本文介绍了如何下载最新的Mesos,并在不同的系统上安装Mesos</description>

##入门
###下载 Mesos
这里有不同的方式获取 Mesos:

1\. 从[ Apache](http://mesos.apache.org/downloads/)下载最新稳定版(推荐)。


	$ wget http://www.apache.org/dist/mesos/0.27.2/mesos-0.27.2.tar.gz
	$ tar -zxf mesos-0.27.2.tar.gz

2\. 克隆Mesos的Git库(适合高级用户)。


	$ git clone https://git-wip-us.apache.org/repos/asf/mesos.git


*注：如果你在运行上述命令时遇到问题，你可能需要先安装 `wget`, `tar`, `git` 这些工具。可以参考***系统要求***部分。*

###系统要求

Mesos 可以运行在 Linux (64位)和 Mac OS X（64位）。如果要从源代码编译 Mesos ,则 GCC 4.8.1+ 或 Clang 3.5+ 是必须的。

完全支持 Linux 下的进程隔离，内核版本要不低于 3.10

确保你的主机名可以通过 DNS 或 `/etc/hosts` 解析，从而可以完全支持 Docker 的 host-networking 功能。这在一些 Mesos 测试中会被用到。如有疑问，请确认 `/etc/hosts` 中包含你的主机名。

####Ubuntu14.04
以下是 Ubuntu 14.04现有的说明。如果您使用不同的版本，请安装相应的的软件包。

	# Update the packages.
	$ sudo apt-get update

	# Install a few utility tools.
	$ sudo apt-get install -y tar wget git

	# Install the latest OpenJDK.
	$ sudo apt-get install -y openjdk-7-jdk

	# Install autotools (Only necessary if building from git repository).
	$ sudo apt-get install -y autoconf libtool

	# Install other Mesos dependencies.
	$ sudo apt-get -y install build-essential python-dev python-boto libcurl4-nss-dev libsasl2-dev libsasl2-modules maven libapr1-dev libsvn-dev

####Mac OS X Yosemite 和 El Capitan

以下是在 Mac OS X Yosemite 和 El Capitan 下的安装说明。如果您使用不同的版本，请下载相应的软件包。

	# Install Command Line Tools.
	$ xcode-select --install

	# Install Homebrew.
	$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

	# Install Java.
	$ brew install Caskroom/cask/java

	# Install libraries.
	$ brew install wget git autoconf automake libtool subversion maven

*注：当你从 Yosemite 升级到 El Capitan ，请在升级后重新运行 `xcode-select --install`。*

####CentOS 6.6
以下是在 CentOS 6.6版本下的说明。如果您使用不同的版本，请下载相应的软件包。

	# Install a recent kernel for full support of process isolation.
	$ sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
	$ sudo rpm -Uvh http://www.elrepo.org/elrepo-release-6-6.el6.elrepo.noarch.rpm
	$ sudo yum --enablerepo=elrepo-kernel install -y kernel-lt

	# Make the just installed kernel the one booted by default, and reboot.
	$ sudo sed -i 's/default=1/default=0/g' /boot/grub/grub.conf
	$ sudo reboot

	# Install a few utility tools. This also forces an update of `nss`,
	# which is necessary for the Java bindings to build properly.
	$ sudo yum install -y tar wget git which nss

	# 'Mesos > 0.21.0' requires a C++ compiler with full C++11 support,
	# (e.g. GCC > 4.8) which is available via 'devtoolset-2'.
	# Fetch the Scientific Linux CERN devtoolset repo file.
	$ sudo wget -O /etc/yum.repos.d/slc6-devtoolset.repo http://linuxsoft.cern.ch/cern/devtoolset/slc6-devtoolset.repo

	# Import the CERN GPG key.
	$ sudo rpm --import http://linuxsoft.cern.ch/cern/centos/7/os/x86_64/RPM-GPG-KEY-cern

	# Fetch the Apache Maven repo file.
	$ sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo

	# 'Mesos > 0.21.0' requires 'subversion > 1.8' devel package, which is
	# not available in the default repositories.
	# Create a WANdisco SVN repo file to install the correct version:
	$ sudo cat > /etc/yum.repos.d/wandisco-svn.repo <<EOF
	[WANdiscoSVN]
	name=WANdisco SVN Repo 1.8
	enabled=1
	baseurl=http://opensource.wandisco.com/centos/6/svn-1.8/RPMS/$basearch/
	gpgcheck=1
	gpgkey=http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
	EOF

	# Install essential development tools.
	$ sudo yum groupinstall -y "Development Tools"

	# Install 'devtoolset-2-toolchain' which includes GCC 4.8.2 and related packages.
	$ sudo yum install -y devtoolset-2-toolchain

	# Install other Mesos dependencies.
	$ sudo yum install -y apache-maven python-devel java-1.7.0-openjdk-devel zlib-devel libcurl-devel openssl-devel cyrus-sasl-devel cyrus-sasl-md5 apr-devel subversion-devel apr-util-devel

	# Enter a shell with 'devtoolset-2' enabled.
	$ scl enable devtoolset-2 bash
	$ g++ --version  # Make sure you've got GCC > 4.8!

	# Process isolation is using cgroups that are managed by 'cgconfig'.
	# The 'cgconfig' service is not started by default on CentOS 6.6.
	# Also the default configuration does not attach the 'perf_event' subsystem.
	# To do this, add 'perf_event = /cgroup/perf_event;' to the entries in '/etc/cgconfig.conf'.
	$ sudo yum install -y libcgroup
	$ sudo service cgconfig start

####CentOS 7.1
以下是在 CentOS 7.1版本下的说明。如果您使用不同的版本，请下载相应的软件包。

	# Install a few utility tools
	$ sudo yum install -y tar wget git

	# Fetch the Apache Maven repo file.
	$ sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo

	# Install the EPEL repo so that we can pull in 'libserf-1' as part of our
	# subversion install below.
	$ sudo yum install -y epel-release

	# 'Mesos > 0.21.0' requires 'subversion > 1.8' devel package,
	# which is not available in the default repositories.
	# Create a WANdisco SVN repo file to install the correct version:
	$ sudo cat > /etc/yum.repos.d/wandisco-svn.repo <<EOF
	[WANdiscoSVN]
	name=WANdisco SVN Repo 1.9
	enabled=1
	baseurl=http://opensource.wandisco.com/centos/7/svn-1.9/RPMS/$basearch/
	gpgcheck=1
	gpgkey=http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
	EOF

	# Parts of Mesos require systemd in order to operate. However, Mesos
	# only supports versions of systemd that contain the 'Delegate' flag.
	# This flag was first introduced in 'systemd version 218', which is
	# lower than the default version installed by centos. Luckily, centos
	# 7.1 has a patched 'systemd < 218' that contains the 'Delegate' flag.
	# Explicity update systemd to this patched version.
	$ sudo yum update systemd

	# Install essential development tools.
	$ sudo yum groupinstall -y "Development Tools"

	# Install other Mesos dependencies.
	$ sudo yum install -y apache-maven python-devel java-1.8.0-openjdk-devel zlib-devel libcurl-devel openssl-devel cyrus-sasl-devel cyrus-sasl-md5 apr-devel subversion-devel apr-util-devel

####构建 Mesos

```
	# Change working directory.
    $ cd mesos

    # Bootstrap (Only required if building from git repository).
    $ ./bootstrap

    # Configure and build.
    $ mkdir build
    $ cd build
    $ ../configure
    $ make
```

为了加快构建以及减少冗长的 log ，你可以在 `make` 命令后添加 `-j \<number of cores\> V=0`

```
	# Run test suite.
    $ make check

    # Install (Optional).
    $ make install
```

####示例

Mesos 包含了用 Java , C++ , Python 写的 frameworks 示例。frameworks 示例的二进制文件只有在运行了`make check`之后才可用。

```
 	# Change into build directory.
    $ cd build

    # Start mesos master (Ensure work directory exists and has proper permissions).
    $ ./bin/mesos-master.sh --ip=127.0.0.1 --work_dir=/var/lib/mesos

    # Start mesos slave.
    $ ./bin/mesos-slave.sh --master=127.0.0.1:5050

    # Visit the mesos web page.
    $ http://127.0.0.1:5050

    # Run C++ framework (Exits after successfully running some tasks.).
    $ ./src/test-framework --master=127.0.0.1:5050

    # Run Java framework (Exits after successfully running some tasks.).
    $ ./src/examples/java/test-framework 127.0.0.1:5050

    # Run Python framework (Exits after successfully running some tasks.).
    $ ./src/examples/python/test-framework 127.0.0.1:5050
```
*注意：这些示例假设你是在本地机器上运行 Mesos 。但在生产环境（如 AWS ）中，按照上面的示例操作，你将无法访问 Mesos 的 web 界面。如果想访问 Mesos 页面，你需要在启动 Mesos Master 时为主机分配一个真实 IP 地址，并确保你的防火墙打开了 5050 端口的外部访问权限。*

>本篇内容翻译自[http://mesos.apache.org/gettingstarted/](http://mesos.apache.org/gettingstarted/)
