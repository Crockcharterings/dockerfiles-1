FROM xataz/nginx-php:latest

MAINTAINER arckosfr <contact@lemark.xyz>

LABEL version="201705261" \
      tags="latest"

ENV UID=991 GID=991 VER=2.17 CRON_PERIOD=15m

ADD https://github.com/SSilence/selfoss/releases/download/${VER}/selfoss-${VER}.zip /tmp/

RUN apk add --update \
	      unzip && \
  	mkdir -p /selfoss /etc/s6.d/.s6-svscan && \
  	unzip /tmp/selfoss-*.zip -d /selfoss && \
  	cp -R /selfoss/data /selfoss/bkp &&\
        rm -rf /var/cache/apk/* /tmp/*  && apk del unzip

COPY rootfs /
RUN chmod +x /usr/local/bin/startup /etc/s6.d/*/*

EXPOSE 8080
VOLUME /selfoss/data

ENTRYPOINT ["/usr/local/bin/startup"]
CMD ["/bin/s6-svscan", "/etc/s6.d"]
