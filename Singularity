bootstrap:docker
From:centos:7

%labels

MAINTAINER	Andreas Haupt <andreas.haupt@desy.de>
NAME		CTA-Dirac client

%environment

#export PATH=/opt/dirac/scripts:/opt/dirac/Linux_x86_64_glibc-2.17/bin
#export LD_LIBRARY_PATH=/opt/dirac/Linux_x86_64_glibc-2.17/lib/mysql:/opt/dirac/Linux_x86_64_glibc-2.17/lib:
#export PYTHONPATH=/opt/dirac
#export PYTHONUNBUFFERED=yes
#export PYTHONOPTIMIZE=x
export X509_VOMS_DIR=/opt/dirac/etc/grid-security/vomsdir
export SSL_CERT_DIR=/opt/dirac/etc/grid-security/certificates
export REQUESTS_CA_BUNDLE=/opt/dirac/etc/grid-security/certificates
export DIRAC=/opt/dirac
export DIRACBIN=/opt/dirac/Linux_x86_64_glibc-2.17/bin
export DIRACSCRIPTS=/opt/dirac/scripts
export DIRACLIB=/opt/dirac/Linux_x86_64_glibc-2.17/lib
export DIRACPLAT=Linux_x86_64_glibc-2.17
export GFAL_CONFIG_DIR=/opt/dirac/Linux_x86_64_glibc-2.17/etc/gfal2.d
export GFAL_PLUGIN_DIR=/opt/dirac/Linux_x86_64_glibc-2.17/lib/gfal2-plugins
export GLOBUS_IO_IPV6=TRUE
export GLOBUS_FTP_CLIENT_IPV6=TRUE
export ARC_PLUGIN_PATH=/opt/dirac/Linux_x86_64_glibc-2.17/lib/arc
#export TERMINFO=/opt/dirac/Linux_x86_64_glibc-2.17/share/terminfo:/usr/share/terminfo:/etc/terminfo
#export RRD_DEFAULT_FONT=/opt/dirac/Linux_x86_64_glibc-2.17/share/rrdtool/fonts/DejaVuSansMono-Roman.ttf


%post

# general packages needed inside the container
yum -y install epel-release less strace wget
# packages Dirac depends on
yum -y install boost-program-options boost-python boost-system boost-thread c-ares lfc-libs libtool-ltdl protobuf

export DIRAC_ROOT=/opt/dirac

mkdir -p $DIRAC_ROOT
cd $DIRAC_ROOT
cat << EOF > $DIRAC_ROOT/defaults-CTA.cfg
LocalInstallation
{
  ConfigurationServer = dips://ccdcta-server03.in2p3.fr:9135/Configuration/Server, dips://ccdcta-server02.in2p3.fr:9135/Configuration/Server, dips://dcta-agents01.pic.es:9135/Configuration/Server, dips://dcta-servers01.pic.es:9135/Configuration/Server
  VirtualOrganization = vo.cta.in2p3.fr
  Setup = CTA
  PythonVersion = 27
  Project = CTA
  InstallType = client
  SkipCAChecks = True
  Release = v1r42p1
  LcgVer = 2017-01-27
  SkipCADownload = True
}
EOF
wget https://github.com/DIRACGrid/DIRAC/raw/master/Core/scripts/dirac-install.py
python dirac-install.py -V CTA

mkdir -p $DIRAC_ROOT/etc
cat <<EOF > $DIRAC_ROOT/etc/dirac.cfg
LocalInstallation
{
  ConfigurationServer = dips://ccdcta-server03.in2p3.fr:9135/Configuration/Server
  ConfigurationServer += dips://ccdcta-server02.in2p3.fr:9135/Configuration/Server
  ConfigurationServer += dips://dcta-agents01.pic.es:9135/Configuration/Server
  ConfigurationServer += dips://dcta-servers01.pic.es:9135/Configuration/Server
  VirtualOrganization = vo.cta.in2p3.fr
  Setup = CTA
  PythonVersion = 27
  Project = CTA
  InstallType = client
  Extensions = COMDIRAC
  SkipCAChecks = True
  Release = v1r42p1
  LcgVer = 2017-01-27
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
  }
  Setup = CTA
  VirtualOrganization = vo.cta.in2p3.fr
  Extensions = COMDIRAC
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
/C=FR/O=CNRS/CN=GRID2-FR
EOF

chown -R root:root $DIRAC_ROOT

%runscript

exec /bin/bash "$@"
