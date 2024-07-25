FROM nginx:1.19

ARG UID=1000
EXPOSE 80

RUN adduser -u ${UID} --disabled-password --gecos "" appuser

COPY docker/nginx/default.conf.template /etc/nginx/conf.d/default.conf.template
COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
