FROM debian:jessie

RUN groupadd -r bitcoin && useradd -r -m -g bitcoin bitcoin

RUN set -ex \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends ca-certificates wget \
	&& rm -rf /var/lib/apt/lists/*

# grab gosu for easy step-down from root
RUN set -ex \
	&& gpg --keyserver keyserver.ubuntu.com --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
	&& wget -qO /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.7/gosu-$(dpkg --print-architecture)" \
	&& wget -qO /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/1.7/gosu-$(dpkg --print-architecture).asc" \
	&& gpg --verify /usr/local/bin/gosu.asc \
	&& rm /usr/local/bin/gosu.asc \
	&& chmod +x /usr/local/bin/gosu

ENV BITCOIN_VERSION 0.14.2-uasfsegwit0.3
ENV BITCOIN_URL https://uasf.bitcoinreminder.com/core-0.14.2-uasfsegwit0.3/bitcoin-0.14.2-bip148_segwit0.3-x86_64-linux-gnu.tar.gz
ENV BITCOIN_SHA256 668ba1ae6d54307878e1e02d396b96e0bb3829b71735cb1992910cadf861db1f
ENV BITCOIN_ASC_URL https://uasf.bitcoinreminder.com/core-0.14.2-uasfsegwit0.3/SHA256SUMS.asc
ENV BITCOIN_PGP_KEY E463A93F5F3117EEDE6C7316BD02942421F4889F

# install bitcoin binaries
RUN set -ex \
	&& BITCOIN_DIST=$(basename $BITCOIN_URL) \
	&& wget -qO $BITCOIN_DIST $BITCOIN_URL \
	&& echo "$BITCOIN_SHA256 $BITCOIN_DIST" | sha256sum -c - \
	&& gpg --keyserver keyserver.ubuntu.com --recv-keys $BITCOIN_PGP_KEY \
	&& wget -qO bitcoin.asc $BITCOIN_ASC_URL \
	&& gpg --verify bitcoin.asc \
	&& tar -xzvf $BITCOIN_DIST -C /usr/local --strip-components=1 --exclude=*-qt \
	&& rm bitcoin*

ENV BITCOIN_DATA /data
RUN mkdir $BITCOIN_DATA \
	&& chown bitcoin:bitcoin $BITCOIN_DATA \
	&& ln -s $BITCOIN_DATA /home/bitcoin/.bitcoin
VOLUME /data

COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 8332 8333 18332 18333
CMD ["bitcoind"]