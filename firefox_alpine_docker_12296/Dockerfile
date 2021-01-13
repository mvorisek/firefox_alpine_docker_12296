FROM atk4/image:7.4

RUN php --version && composer --version

# fix "localhost" DNS resolution (remove "search" kw from /etc/resolv.conf if present)
RUN sed '/^search /d' /etc/resolv.conf > /tmp/resolv.conf && cat /tmp/resolv.conf > /etc/resolv.conf && rm /tmp/resolv.conf

# install test project
RUN git clone https://github.com/atk4/ui.git
WORKDIR /ui
RUN composer update && php demos/_demo-data/create-sqlite-db.php

# install Selenium
RUN apk add -q openjdk8-jre-base \
    && curl --fail --silent --show-error -L "https://selenium-release.storage.googleapis.com/3.141/selenium-server-standalone-3.141.59.jar" -o /tmp/selenium-server-standalone.jar

# install Firefox
RUN apk add -q xvfb ttf-freefont \
    && apk add -q firefox \
    && curl --fail --silent --show-error -L "https://github.com/mozilla/geckodriver/releases/download/v0.28.0/geckodriver-v0.28.0-linux64.tar.gz" -o /tmp/geckodriver.tar.gz \
    && tar -C /opt -zxf /tmp/geckodriver.tar.gz && rm /tmp/geckodriver.tar.gz \
    && chmod 755 /opt/geckodriver && ln -s /opt/geckodriver /usr/bin/geckodriver

# setup env
RUN addgroup browser && adduser browser -G browser -D -s /bin/sh \
    && touch /var/log/selenium.log && chown browser /var/log/selenium.log

# setup config
RUN sed -E -i 's~(base_url: '"'"'?)(https?://)?[^'"'"'/]+/?~\1http://127.0.0.1:2080/~' behat.yml.dist \
    && sed -i 's/chrome/firefox/' behat.yml.dist \
    && sed -i 's/selenium-firefox/127.0.0.1/' behat.yml.dist \
    && cat behat.yml.dist

# run testing
RUN (php -d open_basedir="$PWD:/tmp" -S 127.0.0.1:2080 -t "$PWD" > /var/log/webserver.log 2>&1 &) \
    && { Xvfb -ac :99 -screen 0 1920x1200x24 & } && export DISPLAY=:99 \
    && su browser -c 'java -Dwebdriver.chrome.whitelistedIps=127.0.0.1 -jar /tmp/selenium-server-standalone.jar -role standalone -host 127.0.0.1 -port 4444 -sessionTimeout 15 -browserTimeout 12 > /var/log/selenium.log 2>&1 &' \
    && sleep 2 \
    && vendor/bin/behat --config behat.yml.dist -vv --colors --stop-on-failure || (sleep 1; ps aux; killall java; cat /var/log/selenium.log; exit 1)
