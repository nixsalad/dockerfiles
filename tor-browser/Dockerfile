#
# Usage:
#
#  docker build . -t tor-browser
#  docker run --log-driver none --rm -v /dev/shm:/dev/shm -v /tmp/.X11-unix:/tmp/.X11-unix:ro -e DISPLAY=$DISPLAY tor-browser
#
# Note: x11 may require: xhost +"local:docker@"
# Note: Wayland require: x11docker tor-browser 

FROM debian:trixie-slim

RUN apt-get update && apt-get install -y curl gnupg file xz-utils \
	libgtk-3.0 libdbus-glib-1-2 libx11-xcb-dev libxt6 libasound2

# INCREMENT TOR_VERSION ON NEW VERSION RELEASES, LEAVE FINGERPRINT ALONE
ENV TOR_VERSION="14.5a4" \
	FINGERPRINT="EF6E286DDA85EA2A4BA7DE684E2C6E8793298290"

# setup tor install path
WORKDIR /opt/tor

# download torbrowser
RUN curl -s https://dist.torproject.org/torbrowser/${TOR_VERSION}/tor-browser-linux-x86_64-${TOR_VERSION}.tar.xz -o torbrowser.tar.xz

# download checksum
RUN curl -s https://dist.torproject.org/torbrowser/${TOR_VERSION}/tor-browser-linux-x86_64-${TOR_VERSION}.tar.xz.asc -o torbrowser.tar.xz.asc

# verify checksum
RUN gpg --auto-key-locate nodefault,wkd --locate-keys torbrowser@torproject.org && \
        gpg --output ./tor.keyring --export ${FINGERPRINT} && \
        gpgv --keyring ./tor.keyring /opt/tor/torbrowser.tar.xz.asc /opt/tor/torbrowser.tar.xz 


# extract tor-browser 
RUN tar --strip 1 -xJf torbrowser.tar.xz tor-browser/Browser

ENV LANG=C.UTF-8

# switch user
RUN useradd anon 
RUN chown -R anon:anon /opt/tor
USER anon

# run tor-browser
ENTRYPOINT ["/bin/bash"]
CMD ["/opt/tor/Browser/start-tor-browser", "--log", "/dev/stdout" ]
