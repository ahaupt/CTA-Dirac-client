bootstrap:docker
From:centos:7

%post

yum -y install wget

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
  SkipCADownload = False
}
EOF
wget https://github.com/DIRACGrid/DIRAC/raw/master/Core/scripts/dirac-install.py
python dirac-install.py -V CTA

# as we do not have a grid certificate here, we need to this step manually:
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
