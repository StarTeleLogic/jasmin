FROM debian:jessie

MAINTAINER Mukesh Kumar <mukesh@startelelogic.co.in>

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r jasmin && useradd -r -g jasmin jasmin

ENV JASMIN_VERSION 0.9.31

# Install requirements
RUN apt-get update && apt-get install -y \
    python2.7 \
    python-pip \
    python-dev \
    libffi-dev \
    libssl-dev \
    rabbitmq-server \
    redis-server \
    supervisor \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
RUN apt-get update && \
      apt-get -y install sudo
RUN sudo pip install --upgrade setuptools
RUN sudo pip install x509
RUN sudo apt-get install libssl-dev
RUN sudo pip install service_identity
RUN apt-get update && apt-get install -y git

# Clone the conf files into the docker container
RUN git clone -b 0.9.31 https://github.com/surendratiwari3/jasmin.git


# Install Jasmin SMS gateway
RUN mkdir -p /etc/jasmin/resource \
    /etc/jasmin/store \
    /var/log/jasmin \
  && chown jasmin:jasmin /etc/jasmin/store \
    /var/log/jasmin \
  && cd jasmin \
  && python setup.py install

# Change binding host for jcli
RUN sed -i '/\[jcli\]/a bind=0.0.0.0' /etc/jasmin/jasmin.cfg

EXPOSE 2775 8990 1401
VOLUME ["/var/log/jasmin", "/etc/jasmin", "/etc/jasmin/store"]

COPY docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["jasmind.py", "--enable-interceptor-client", "--enable-dlr-thrower", "--enable-dlr-lookup", "-u", "jcliadmin", "-p", "jclipwd"]
# Notes:
# - jasmind is started with native dlr-thrower and dlr-lookup threads instead of standalone processes
# - restapi (0.9rc16+) is not started in this docker configuration
