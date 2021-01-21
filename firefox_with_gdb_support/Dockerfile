FROM alpine:3.13.0

# install Behat/Firefox libs
RUN apk add -q xvfb ttf-freefont

# download/patch Firefox
RUN apk add alpine-sdk sudo
RUN git clone https://gitlab.alpinelinux.org/alpine/aports.git && cd aports && git reset --hard v3.13.0
COPY ff_for_gdb.patch aports/
RUN cd aports && git apply ff_for_gdb.patch
WORKDIR aports/community/firefox

# build and install Firefox
# based on https://wiki.alpinelinux.org/wiki/Creating_an_Alpine_package
RUN adduser bb -G abuild -D -s /bin/sh && echo "bb  ALL=(ALL) ALL" >> /etc/sudoers
RUN chmod -R a+w ./ && mkdir -p /var/cache/distfiles && chmod a+w /var/cache/distfiles
RUN abuild-keygen -a -i && chmod 0755 /root /root/.abuild && chmod 0444 /root/.abuild/*.rsa && echo "PACKAGER_PRIVKEY=$(ls /root/.abuild/*.rsa | head -n1)" >> /etc/abuild.conf
RUN su bb -c 'abuild checksum && abuild -r'

RUN su bb -c 'ls -lah ~/packages/community/x86_64'
