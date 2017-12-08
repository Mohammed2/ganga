FROM hepsw/cvmfs-lhcb:latest
LABEL maintainer "Alexander Richards <a.richards@imperial.ac.uk>"

RUN yum install -y wget git python-virtualenv

WORKDIR /root

COPY . ganga

RUN export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/cvmfs/lhcb.cern.ch/lib/lcg/releases/LCG_88/Python/2.7.13/x86_64-slc6-gcc49-opt/lib &&\
    virtualenv -p /cvmfs/lhcb.cern.ch/lib/lcg/releases/LCG_88/Python/2.7.13/x86_64-slc6-gcc49-opt/bin/python venv &&\
    . venv/bin/activate &&\
    pip install --upgrade pip setuptools &&\
    pip install -e ganga &&\
    cd ganga &&\
    pip install --upgrade -r requirements.txt

#ENTRYPOINT . /etc/cvmfs/run-cvmfs.sh && \
#           . ~/.bashrc && \
ENTRYPOINT export X509_CERT_DIR=/cvmfs/lhcb.cern.ch/etc/grid-security/certificates && \
           export X509_VOMS_DIR=/cvmfs/lhcb.cern.ch/etc/grid-security/vomsdir && \
           export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$(dirname $(dirname `which python`))/lib && \
           export GANGA_CONFIG_PATH=GangaLHCb/LHCb.ini && \
           export GANGA_SITE_CONFIG_AREA=/cvmfs/lhcb.cern.ch/lib/GangaConfig/config && \
           lhcb-proxy-init && \
           . venv/bin/activate && \
           cd ganga && \
           pytest python/GangaLHCb/test/Unit --runexternals --cov-report xml --cov . --junitxml tests.xml
