bootstrap:docker
From:centos:7

%post

# general packages needed inside the container
yum -y install less strace wget
# packages Dirac depends on
yum -y install boost-python

export DIRAC_ROOT=/opt/dirac

mkdir -p $DIRAC_ROOT
cd $DIRAC_ROOT
cat << EOF > $DIRAC_ROOT/defaults-CTA.cfg
LocalInstallation
{
  ConfigurationServer = dips://ccdcta-server01.in2p3.fr:9135/Configuration/Server, dips://ccdcta-server02.in2p3.fr:9135/Configuration/Server, dips://dcta-agents01.pic.es:9135/Configuration/Server, dips://dcta-servers01.pic.es:9135/Configuration/Server
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
  ConfigurationServer = dips://ccdcta-server01.in2p3.fr:9135/Configuration/Server
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
    Servers = dips://ccdcta-server01.in2p3.fr:9135/Configuration/Server
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

mkdir -p $DIRAC_ROOT/etc/grid-security/vomses
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

source /opt/dirac/bashrc
