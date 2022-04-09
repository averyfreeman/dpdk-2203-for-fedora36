
# dpdk for openvswitch
### on Fedora 36 workstation beta
---

was having issues with openvswitch complaining about dpdk dependencies, needed kernel modules version so.22 from dpdk release 22.03

[latest issue announces version available: ](https://bugzilla.redhat.com/show_bug.cgi?id=1991248)

but still not compiled through official sources

I'm new to Fedora so I'm just going to throw this up on my github site and worry about developing through the proper channels later.  I'm sure the package maintainer has a better idea of what features and dependencies they need to keep current. Perhaps this work could help if it's already done (they probably have a CI/CD pipeline it needs to be thrown on).

[got rpmbuild from repo: ](https://src.fedoraproject.org/rpms/dpdk.git)

[spec: ](https://src.fedoraproject.org/rpms/dpdk/blob/f36/f/dpdk.spec)

tried `meson`, `ninja` from PyPi but not very intuitive how to use for `rpmbuild`, so just installed the rpms (almost just as current, anyway)

Building is actually really easy. Clone your rpm source, change the `.spec` file to reflect version `22.03`.

Get all the dependencies - 

I did this in toolbox, which is the most awesome new-ish thing.  You have an accessible filesystem container from podman you can use for develpment that has full access to your home dir, and dbus, etc. 

That way you can blow the container away afterwards if you don't want to keep all the little dependency files you only really need to compile this software.  Or, if you're concerned about dependencies conflicting with other build dependencies in the future, as you should be because traditionally it would happen quite frequently after some time of developing software, you can make another toolbox container for each software type going forward - e.g. one for Qt6, one for kernels, one for your nodejs projects, one for java, and so on ...

toobox: `dnf install -y toolbox`; `toolbox create`; `toolbox enter`

set whatever `PATH env` or whatever you need to

build environment:

```
dnf groupinstall \
  "C Development Tools and Libraries" \
  "Development Tools"
```

rpm build dependencies (required):

```
dnf install -y \
   ninja-build python3-devel python3-pyelftools \
   meson  libatomic openssl-devel rdma-core-devel \
   fedora-packager rpmdevtools
```

additional dependencies I installed for features - not sure how much of this is installed.  I got these dependencies from configuring the raw source and analysing the output:

```
dnf install -y \
  librtprocess-devel librttopo-devel \
  librttr-devel intel-ipsec-mb-devel \
  crypto-policies cmake libbpf-devel \
  crypto-devel pcapdiff libpcap-devel \
  intel-ipp-crypto-mb-devel crypto-devel \
  libbpf-static libbpf-tools libbpf-cargo \
  numactl-devel libxdp-devel libxdp-static \
  xdp-tools libbsd-devel libbsd-ctor-static \
  python3-pypcapkit libfdt-devel jansson-devel \
  libarchive-devel
 ```

This is documentation stuff - was kind of thrown in haphazardly, but it gernerated a noarch docs rpm:

```
dnf install -y \
  python3-sphinx-theme-py3doc-enhanced \
  python3-ansible-pygments python3-breathe \
  python3-sphinx python3-sphinxext-opengraph \
  python3-numpydoc 
```

Put the [22.03 original source code](https://fast.dpdk.org/rel/dpdk-22.03.tar.xz) in `/home/username/rpmbuild/SOURCES/name-of-tar.gz` (easiest to just `wget` it there)

I've included built packages since who really wants to have to build this stuff themselves. amd64 only.