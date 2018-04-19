FROM fedora:27

ARG MNT="dmitry sevostyanov"

MAINTAINER $MNT

ARG YUM=dnf

#	dbus-x11 \
RUN \
    $YUM install -y --setopt=tsflags=nodocs \
	hostname \
	jemalloc \
	libnfsidmap \
	nfs-ganesha \
	nfs-ganesha-vfs \
	nfs-utils \
	rpcbind \
	xfsprogs \
    && \
    $YUM clean all

RUN \
    mkdir -p /var/run/dbus && \
    mkdir -p /export ; \
    pwd ; ls -lR /tmp /opt

# expose mountd 20048/tcp and nfsd 2049/tcp and rpcbind 111/tcp 111/udp
EXPOSE 2049/tcp 20048/tcp 111/tcp 111/udp

#ENTRYPOINT ["/nfs-provisioner"]
