FROM debian:stretch

ENV REFRESHED_AT 2017-12-06
ENV SWAN_VER 3.22

WORKDIR /opt/src

RUN apt-get -yqq update \
    && DEBIAN_FRONTEND=noninteractive \
       apt-get -yqq --no-install-recommends install \
         wget dnsutils openssl ca-certificates kmod \
         iproute gawk grep sed net-tools iptables \
         bsdmainutils libcurl3-nss \
         libnss3-tools libevent-dev libcap-ng0 xl2tpd \
         libnss3-dev libnspr4-dev pkg-config libpam0g-dev \
         libcap-ng-dev libcap-ng-utils libselinux1-dev \
         libcurl4-nss-dev flex bison gcc make \
    && wget -t 3 -T 30 -nv -O "libreswan.tar.gz" "https://github.com/libreswan/libreswan/archive/v${SWAN_VER}.tar.gz" \
    || wget -t 3 -T 30 -nv -O "libreswan.tar.gz" "https://download.libreswan.org/libreswan-${SWAN_VER}.tar.gz" \
    && tar xzf "libreswan.tar.gz" \
    && rm -f "libreswan.tar.gz" \
    && cd "libreswan-${SWAN_VER}" \
    && { [ "$SWAN_VER" = "3.22" ] && sed -i '/^#define LSWBUF_CANARY/s/-2$/((char) -2)/' include/lswlog.h || true; } \
    && printf 'WERROR_CFLAGS =\nUSE_DNSSEC = false\nUSE_SYSTEMD_WATCHDOG = false\n' > Makefile.inc.local \
    && make -s base \
    && make -s install-base \
    && cd /opt/src \
    && rm -rf "/opt/src/libreswan-${SWAN_VER}" \
    && apt-get -yqq remove \
         libnss3-dev libnspr4-dev pkg-config libpam0g-dev \
         libcap-ng-dev libcap-ng-utils libselinux1-dev \
         libcurl4-nss-dev flex bison gcc make \
         perl-modules perl \
    && apt-get -yqq autoremove \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/*

COPY run.sh /usr/sbin/run.sh
COPY VPN.mobileconfig.tpl /opt/src/
RUN chmod 755 /usr/sbin/run.sh

EXPOSE 500/udp 4500/udp

VOLUME ["/lib/modules"]

CMD ["/usr/sbin/run.sh"]
