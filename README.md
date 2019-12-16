CTA-Dirac-client
==================

Singularity image containing CTA-Dirac client

Prerequsites
============

Your client must have access to the IGTF trust anchor directory. Environment variable X509_CERT_DIR must point to it.

Usage
=====

```
[notebook] ~ % cd /tmp 
[notebook] /tmp % singularity pull shub://ahaupt/CTA-Dirac-client
Progress |===================================| 100.0% 
Done. Container is at: /tmp/ahaupt-CTA-Dirac-client-master-latest.simg
[notebook] /tmp % singularity shell -B $X509_CERT_DIR:/opt/dirac/etc/grid-security/certificates /tmp/ahaupt-CTA-Dirac-client-master-latest.simg
Singularity: Invoking an interactive shell within container...

Singularity ahaupt-CTA-Dirac-client-master-latest.simg:/tmp> dirac-version 
v6r19p10
Singularity ahaupt-CTA-Dirac-client-master-latest.simg:/tmp> 
```
