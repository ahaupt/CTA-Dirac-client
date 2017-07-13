bootstrap:docker
From:centos:7

%post

mkdir -p /opt/dirac
cd /opt/dirac
cat << EOF > /opt/dirac/defaults-CTA.cfg
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
