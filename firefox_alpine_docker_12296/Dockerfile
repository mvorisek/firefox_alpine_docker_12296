FROM alpine

# install Firefox
RUN apk add -q xvfb ttf-freefont firefox

# setup env
RUN addgroup browser && adduser browser -G browser -D -s /bin/sh

# run testing
RUN { Xvfb -ac :99 -screen 0 1920x1200x24 & } && export DISPLAY=:99 && sleep 2 \
    && timeout 10 su browser -c 'firefox --headless --window-size=1920,1200 https://ui.agiletoolkit.org/demos/form/form.php'
