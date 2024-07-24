FROM nginx:1.19

ARG UID=1000
EXPOSE 80

RUN adduser -u ${UID} --disabled-password --gecos "" appuser

COPY default.conf /etc/nginx/conf.d/
