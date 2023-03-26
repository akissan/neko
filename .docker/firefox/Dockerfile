ARG BASE_IMAGE=m1k1o/neko:base
FROM $BASE_IMAGE

ARG SRC_URL="https://download.mozilla.org/?product=firefox-latest&os=linux64&lang=en-US"

#
# install firefox
RUN set -eux; apt-get update; \
    apt-get install -y --no-install-recommends openbox \
    xz-utils bzip2 libgtk-3-0 libdbus-glib-1-2; \
    #
    # fetch latest release
    wget -O /tmp/firefox-setup.tar.bz2 "${SRC_URL}"; \
    mkdir /usr/lib/firefox; \
    tar -xjf /tmp/firefox-setup.tar.bz2 -C /usr/lib; \
    rm -f /tmp/firefox-setup.tar.bz2; \
    ln -s /usr/lib/firefox/firefox /usr/bin/firefox; \
    #
    # create a profile directory
    mkdir -p /home/neko/.mozilla/firefox/profile.default/extensions; \
    chown -R neko:neko /home/neko/.mozilla/firefox/profile.default; \
    #
    # clean up
    apt-get --purge autoremove -y xz-utils bzip2; \
    apt-get clean -y; \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

#
# copy configuation files
COPY .docker/firefox/supervisord.conf /etc/neko/supervisord/firefox.conf
COPY .docker/firefox/neko.js /usr/lib/firefox/mozilla.cfg
COPY .docker/firefox/autoconfig.js /usr/lib/firefox/defaults/pref/autoconfig.js
COPY .docker/firefox/policies.json /usr/lib/firefox/distribution/policies.json
COPY --chown=neko .docker/firefox/profiles.ini /home/neko/.mozilla/firefox/profiles.ini
COPY .docker/firefox/openbox.xml /etc/neko/openbox.xml

COPY secrets/russian_trusted_root_ca_pem.crt secrets/russian_trusted_sub_ca_pem.crt  /usr/local/share/ca-certificates/
RUN apt-get install -y ca-certificates
RUN update-ca-certificates
