FROM alpine:3.17

ARG TZ='Asia/Shanghai'
ENV DEFAULT_TZ ${TZ}
ENV DISPLAY :1
# alternative 1024x768x16
ENV RESOLUTION 1920x1080x24

RUN apk upgrade --update && \
  apk add --no-cache tzdata && \
  cp /usr/share/zoneinfo/${DEFAULT_TZ} /etc/localtime && \
  date

RUN apk add --no-cache \
  curl wget git vim zip \
  xfce4 faenza-icon-theme xvfb x11vnc \
  exa fd ripgrep \
  ncdu htop \
  firefox-esr lynx \
  bash xfce4-terminal tmux neofetch && \
  apk add --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing \
  novnc && \
  ln -s /usr/share/novnc/vnc.html /usr/share/novnc/index.html

SHELL ["/bin/bash", "-c"]

# setup supervisor
COPY supervisor /tmp
RUN apk add --no-cache supervisor && \
  echo_supervisord_conf > /etc/supervisord.conf && \
  sed -i -r -f /tmp/supervisor.sed /etc/supervisord.conf && \
  mkdir -pv /etc/supervisor/conf.d /var/log/{novnc,x11vnc,xfce4,xvfb} && \
  mv /tmp/supervisor-*.ini /etc/supervisor/conf.d/ && \
  rm -fr /tmp/supervisor*

WORKDIR /root

# update configs
COPY .bashrc .vimrc /root/

CMD ["supervisord", "-c", "/etc/supervisord.conf", "-n"]
