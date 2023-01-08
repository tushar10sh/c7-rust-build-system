# Author Tushar Shukla ( tushar.10sh@gmail.com )
FROM local/c7-systemd:latest as builder
RUN yum update -y 
RUN yum install -y epel-release
RUN yum update -y 
RUN yum install -y curl wget git ninja-build tar bzip2 gcc gcc-c++ make openssl
RUN yum group install -y 'Development Tools'
RUN yum install perl-core zlib-devel -y
COPY openssl-1.1.1c.tar.gz /usr/local/src/
RUN cd /usr/local/src/ && tar zxf openssl-1.1.1c.tar.gz 
RUN cd /usr/local/src/openssl-1.1.1c && ./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib && make && make install 
RUN echo "/usr/local/ssl/lib " >> /etc/ld.so.conf.d/openssl-1.1.1c.conf 
RUN ldconfig -v
RUN mv /bin/openssl /bin/openssl.backup
COPY openssl.sh /etc/profile.d/
RUN chmod +x /etc/profile.d/openssl.sh 
RUN source /etc/profile.d/openssl.sh
RUN yum install -y which
RUN source /etc/profile.d/openssl.sh && which openssl && openssl version -a
RUN groupadd rustdev -g 1000
RUN useradd rustdev -u 1000 -g 1000 
RUN mkdir /opt/rust-local -p
RUN mkdir /home/rustdev/ -p
RUN chown -R rustdev:rustdev /opt
RUN chown -R rustdev:rustdev /home/rustdev
USER rustdev
WORKDIR /home/rustdev/
COPY . /home/rustdev/
RUN sh Miniconda3-latest-Linux-x86_64.sh -b -p /opt/miniconda3
COPY .bashrc /home/rustdev/
RUN ls /home/rustdev/
#RUN tar xf rustc-1.66.0-src.tar.gz
RUN tar xf cmake-3.25.1.tar.gz 
RUN source /etc/profile.d/openssl.sh && cd /home/rustdev/cmake-3.25.1 && sh ./bootstrap --prefix=/opt/rust-local -- -DOPENSSL_ROOT_DIR=/usr/local/ssl -DOPENSSL_LIBRARIES=/usr/local/ssl/lib -DOPENSSL_INCLUDE_DIR=/usr/local/ssl/include  && make -j4 && make install
USER root
RUN chown -R rustdev:rustdev /home/rustdev/.bashrc
USER rustdev
COPY rustup.rs .
RUN mkdir /opt/rust-local/rustup-home
RUN mkdir /opt/rust-local/cargo-home
ENV RUSTUP_HOME /opt/rust-local/rustup-home
ENV CARGO_HOME /opt/rust-local/cargo-home
RUN sh rustup.rs -y --profile complete
#RUN rm -rf rustc-1.66.0-src 
