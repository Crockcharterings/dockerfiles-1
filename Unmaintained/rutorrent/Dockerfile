FROM alpine:edge
MAINTAINER xataz <https://github.com/xataz/dockerfiles>
MAINTAINER Wonderfall <wonderfall@schrodinger.io>

ARG MEDIAINFO_VER=0.7.85
ARG RTORRENT_VER=0.9.6
ARG LIBTORRENT_VER=0.13.6
ARG BUILD_CORES

ENV UID=991 \
    GID=991 \
    WEBROOT=/ \
    PKG_CONFIG_PATH=/usr/local/lib/pkgconfig

RUN echo "@commuedge https://nl.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories \
 && echo "@testing https://nl.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories \
 && NB_CORES=${BUILD_CORES-`getconf _NPROCESSORS_CONF`} \
 && BUILD_DEPS=" \
    build-base \
    git \
    libtool \
    automake \
    autoconf \
    wget \
    tar \
    subversion \
    cppunit-dev \
    openssl-dev \
    ncurses-dev \
    curl-dev \
    binutils" \
 && apk -U add \
    ${BUILD_DEPS} \
    ffmpeg \
    ca-certificates \
    nginx \
    php7@testing \
    php7-fpm@testing \
    php7-json@testing \
    curl \
    gzip \
    zip \
    unrar \
    supervisor \
    geoip \
    tini@commuedge \
 && cd /tmp \
 && wget -q http://downloads.sourceforge.net/mktorrent/mktorrent-1.0.tar.gz \
 && tar xzvf mktorrent-1.0.tar.gz \
 && svn checkout http://svn.code.sf.net/p/xmlrpc-c/code/stable xmlrpc-c \
 && mkdir libtorrent rtorrent \
 && cd libtorrent && wget -qO- https://github.com/rakshasa/libtorrent/archive/${LIBTORRENT_VER}.tar.gz | tar xz --strip 1 \
 && cd ../rtorrent && wget -qO- https://github.com/rakshasa/rtorrent/archive/${RTORRENT_VER}.tar.gz | tar xz --strip 1 \
 && cd /tmp \
 && wget -q http://mediaarea.net/download/binary/mediainfo/${MEDIAINFO_VER}/MediaInfo_CLI_${MEDIAINFO_VER}_GNU_FromSource.tar.gz \
 && wget -q http://mediaarea.net/download/binary/libmediainfo0/${MEDIAINFO_VER}/MediaInfo_DLL_${MEDIAINFO_VER}_GNU_FromSource.tar.gz \
 && tar xzf MediaInfo_DLL_${MEDIAINFO_VER}_GNU_FromSource.tar.gz \
 && tar xzf MediaInfo_CLI_${MEDIAINFO_VER}_GNU_FromSource.tar.gz \
 && tar xzvf mktorrent-1.0.tar.gz \
 && cd /tmp/mktorrent-1.0 && make -j ${NB_CORES} && make install \
 && cd  /tmp/MediaInfo_DLL_GNU_FromSource && ./SO_Compile.sh \
 && cd /tmp/MediaInfo_DLL_GNU_FromSource/ZenLib/Project/GNU/Library && make install \
 && cd /tmp/MediaInfo_DLL_GNU_FromSource/MediaInfoLib/Project/GNU/Library && make install \
 && cd /tmp/MediaInfo_CLI_GNU_FromSource && ./CLI_Compile.sh \
 && cd /tmp/MediaInfo_CLI_GNU_FromSource/MediaInfo/Project/GNU/CLI && make install \
 && cd /tmp/xmlrpc-c && ./configure && make -j ${NB_CORES} && make install \
 && cd /tmp/libtorrent && ./autogen.sh && ./configure \
 && make -j ${NB_CORES} && make install \
 && cd /tmp/rtorrent && ./autogen.sh && ./configure --with-xmlrpc-c \
 && make -j ${NB_CORES} && make install \
 && mkdir -p /var/www && cd /var/www \
 && git clone https://github.com/Novik/ruTorrent.git rutorrent --depth=1 \
 && cd /var/www/rutorrent/plugins/ \
 && git clone https://github.com/Korni22/rutorrent-logoff logoff --depth=1 \
 && git clone https://github.com/xombiemp/rutorrentMobile.git mobile --depth=1 \
 && git clone https://github.com/Ardakilic/rutorrent-pausewebui pausewebui --depth=1 \
 && cd /var/www/rutorrent/plugins/theme/themes \
 && git clone https://github.com/Phlooo/ruTorrent-MaterialDesign.git Material --depth=1 \
 && mv /var/www/rutorrent /var/www/torrent \
 && strip -s /usr/local/bin/rtorrent \
 && strip -s /usr/local/bin/mediainfo \
 && apk del ${BUILD_DEPS} \
 && deluser svn && delgroup svnusers \
 && rm -rf /var/cache/apk/* /tmp/*

COPY rootfs /

RUN chmod +x /usr/bin/*

VOLUME /data /var/www/torrent/share/users
EXPOSE 80 49184 49184/udp

LABEL description="BitTorrent client with WebUI front-end" \
      rtorrent="rTorrent BiTorrent client v$RTORRENT_VER" \
      libtorrent="libtorrent v$LIBTORRENT_VER" \
      mediainfo="mediainfo v$MEDIAINFO_VER" \
	  version="201608161" \
	  tags="latest"

CMD ["/sbin/tini","--","startup"]