FROM ubuntu:22.04
RUN apt-get update && apt-get install -y dnsmasq vim && apt-get clean && rm -rf /var/lib/apt/lists/*
COPY ./dnsmasq.conf /etc/dnsmasq.conf
ENTRYPOINT ["/usr/sbin/dnsmasq","-d"]
