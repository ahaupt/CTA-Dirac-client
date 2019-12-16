bootstrap:docker
From:centos:7

%labels

MAINTAINER	Andreas Haupt <andreas.haupt@desy.de>
NAME		CTA-Dirac client

%environment

source /opt/dirac/bashrc

%post

export DIRAC_ROOT=/opt/dirac

_PYTHON_VERSION=27
_RELEASE=v1r55

# general packages needed inside the container
yum -y install epel-release less strace wget
# packages Dirac depends on
yum -y install boost-program-options boost-python boost-system boost-thread c-ares lfc-libs libtool-ltdl protobuf

mkdir -p $DIRAC_ROOT
cd $DIRAC_ROOT
cat << EOF > $DIRAC_ROOT/defaults-CTA.cfg
LocalInstallation
{
  ConfigurationServer = dips://ccdcta-server03.in2p3.fr:9135/Configuration/Server, dips://ccdcta-server02.in2p3.fr:9135/Configuration/Server, dips://dcta-agents01.pic.es:9135/Configuration/Server, dips://dcta-servers01.pic.es:9135/Configuration/Server
  VirtualOrganization = vo.cta.in2p3.fr
  Setup = CTA
  PythonVersion = ${_PYTHON_VERSION}
  Project = CTA
  InstallType = client
  SkipCAChecks = True
  Release = ${_RELEASE}
  SkipCADownload = True
}
EOF
wget https://github.com/DIRACGrid/DIRAC/raw/master/Core/scripts/dirac-install.py
python dirac-install.py -V CTA

mkdir -p $DIRAC_ROOT/etc
cat <<EOF > $DIRAC_ROOT/etc/dirac.cfg
LocalInstallation
{
  ConfigurationServer = dips://ccdcta-server03.in2p3.fr:9135/Configuration/Server, dips://ccdcta-server02.in2p3.fr:9135/Configuration/Server, dips://dcta-agents01.pic.es:9135/Configuration/Server, dips://dcta-servers01.pic.es:9135/Configuration/Server, dips://cta-dirac.zeuthen.desy.de:9135/Configuration/Server
  VirtualOrganization = vo.cta.in2p3.fr
  Setup = CTA
  PythonVersion = ${_PYTHON_VERSION}
  Project = CTA
  InstallType = client
  Extensions = COMDIRAC, CTA
  Release = ${_RELEASE}
  SkipCAChecks = True
  SkipCADownload = True
}
DIRAC
{
  Configuration
  {
    Servers = dips://ccdcta-server03.in2p3.fr:9135/Configuration/Server
    Servers += dips://ccdcta-server02.in2p3.fr:9135/Configuration/Server
    Servers += dips://dcta-agents01.pic.es:9135/Configuration/Server
    Servers += dips://dcta-servers01.pic.es:9135/Configuration/Server
    Servers += dips://cta-dirac.zeuthen.desy.de:9135/Configuration/Server
  }
  Setup = CTA
  VirtualOrganization = vo.cta.in2p3.fr
  Extensions = COMDIRAC, CTA
  Security
  {
    UseServerCertificate = no
    SkipCAChecks = yes
  }
}
EOF

# as we do not have a grid certificate here, we need to do these steps manually:
# ~$ source bashrc
# ~$ dirac-configure defaults-CTA.cfg

mkdir -p $DIRAC_ROOT/etc/grid-security/{vomses,certificates}
cat <<EOF > $DIRAC_ROOT/etc/grid-security/vomses/vo.cta.in2p3.fr
"vo.cta.in2p3.fr" "cclcgvomsli01.in2p3.fr" "15008" "/O=GRID-FR/C=FR/O=CNRS/OU=CC-IN2P3/CN=cclcgvomsli01.in2p3.fr" "vo.cta.in2p3.fr" "24"
EOF

mkdir -p $DIRAC_ROOT/etc/grid-security/vomsdir/vo.cta.in2p3.fr
cat <<EOF > $DIRAC_ROOT/etc/grid-security/vomsdir/vo.cta.in2p3.fr/cclcgvomsli01.in2p3.fr.lsc
/O=GRID-FR/C=FR/O=CNRS/OU=CC-IN2P3/CN=cclcgvomsli01.in2p3.fr
/C=FR/O=MENESR/OU=GRID-FR/CN=AC GRID-FR Services
EOF

echo "export VOMS_USERCONF=$DIRAC_ROOT/etc/grid-security/vomses" >> $DIRAC_ROOT/bashrc

chown -R root:root $DIRAC_ROOT

%runscript

exec /bin/bash "$@"
