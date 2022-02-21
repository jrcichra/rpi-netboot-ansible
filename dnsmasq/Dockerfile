FROM ubuntu:21.04
RUN apt-get update && apt-get install -y dnsmasq && apt-get clean && rm -rf /var/lib/apt/lists/*
COPY ./dnsmasq.conf /etc/dnsmasq.conf
ENTRYPOINT ["/usr/bin/dnsmasq"]