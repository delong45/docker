FROM ubuntu:14.04
MAINTAINER uverd uve-rd@staff.sina.com.cn
ENV REFRESHED_AT 2016-02-19

# Install required packages
RUN apt-get update
RUN apt-get -y upgrade
RUN apt-get install -y curl vim apache2 supervisor openssh-server net-tools iputils-ping nano make autoconf automake flex bison libtool libevent-dev pkg-config libssl-dev libboost-all-dev libboost-test-dev libboost-program-options-dev libbz2-dev build-essential g++ python-dev git git-core

# Thrift
RUN git clone -b 0.9.1 https://github.com/apache/thrift.git
RUN cd thrift && ./bootstrap.sh && ./configure && make && make install

# Thrift python module
RUN cd thrift/lib/py && python setup.py install

# fb303
RUN cd thrift/contrib/fb303 && ./bootstrap.sh && ./configure && make && make install

# fb303 python module
RUN cd thrift/contrib/fb303/py && python setup.py install && make distclean

# Scribe
RUN git clone https://github.com/facebook/scribe.git
RUN cd scribe && sed -i 's/AM_INIT_AUTOMAKE/#AM_INIT_AUTOMAKE/' configure.ac
RUN cd scribe && ./bootstrap.sh --with-boost-system=boost_system --with-boost-filesystem=boost_filesystem && ./configure CPPFLAGS="-DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -DBOOST_FILESYSTEM_VERSION=3" LIBS="-lboost_system -lboost_filesystem" --with-boost-system=boost_system --with-boost-filesystem=boost_filesystem && make && make install
RUN cd scribe/lib/py && python setup.py install && make distclean

# Sribe conf
RUN mkdir -p /data0/nginx/logs
#just example
RUN cp scribe/examples/example1.conf /usr/local/etc/scribe_example.conf
RUN ldconfig

# Supervisor
RUN mkdir -p /var/lock/apache2 /var/run/apache2 /var/run/sshd /var/log/supervisor
ADD conf/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Expose Port
EXPOSE 1463

# Run scribe
CMD ["/usr/bin/supervisord"]
