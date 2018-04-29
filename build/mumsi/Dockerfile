FROM debian:stretch

ENV HOME /home/user
RUN useradd --create-home --home-dir $HOME user \
	&& chown -R user:user $HOME

# Runtime dependencies for murmurd
RUN apt-get update && apt-get install -y --no-install-recommends \
  ca-certificates \
  build-essential \
  libopus-dev \
  libprotobuf-dev \
#  libboost-dev \
  libboost-regex-dev \
  libboost-system-dev \
  libssl-dev \
  pkgconf \
#  libpjsua2 \
  cmake \
  liblog4cpp5-dev \
  protobuf-compiler \
#  libpjproject-dev \
  libasound2-dev \
  git \
	&& rm -rf /var/lib/apt/lists/*

RUN cd /home/user \
  && mkdir mumsi-dist \
  && cd mumsi-dist \
  && git clone -b develop https://github.com/mrscotty/mumlib.git \
  && mkdir mumlib/build \
  && cd mumlib/build \
  && cmake .. \
  && make \
	&& ls -alh

RUN apt-get update && apt-get install -y \
  wget \
  tar \
	&& rm -rf /var/lib/apt/lists/*

RUN cd /home/user \
  && wget http://www.pjsip.org/release/2.6/pjproject-2.6.tar.bz2 -O pj.tbz2 \
  && tar jxf pj.tbz2 \
	&& cd pjproject-2.6 \
	&& ./configure \
	&& make dep \
	&& make \
	&& make install

RUN cd /home/user \
  && cd mumsi-dist \
  && git clone -b develop https://github.com/mrscotty/mumsi.git \
  && mkdir mumsi/build \
  && cd mumsi/build \
  && cmake .. \
  && make

ADD config.ini /home/user/mumsi-dist/mumsi/build/config.ini
WORKDIR /home/user/mumsi-dist/mumsi/build

ENTRYPOINT ["/home/user/mumsi-dist/mumsi/build/mumsi"]
CMD ["config.ini"]
