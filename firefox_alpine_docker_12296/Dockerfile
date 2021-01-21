FROM alpine:3.13.0

# install Behat/Firefox libs
RUN apk add -q xvfb ttf-freefont

# download/patch Firefox
COPY ../firefox_with_gdb_support/packages ./packages
RUN ls -lah packages/community/x86_64'

# setup env
RUN addgroup browser && adduser browser -G browser -D -s /bin/sh

RUN su bb -c 'cd ~/ && pwd'
RUN su bb -c 'ls -lah ~/packages/community/x86_64'

# run testing
RUN { Xvfb -ac :99 -screen 0 1920x1200x24 & } && export DISPLAY=:99 && sleep 2 \
    && timeout 10 su browser -c 'firefox --headless --window-size=1920,1200 https://ui.agiletoolkit.org/demos/form/form.php'
