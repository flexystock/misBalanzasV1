FROM php:8.2-fpm

# Define el argumento UID con un valor predeterminado
ARG UID=1000
# Exponer el puerto 9000 (puedes ajustar según tus necesidades)
EXPOSE 9000

# Crear el usuario con el UID especificado
RUN adduser -u ${UID} --disabled-password --gecos "" appuser

# Crear y configurar el entorno del usuario
RUN mkdir /home/appuser/.ssh
RUN chown -R appuser:appuser /home/appuser/
RUN echo "StrictHostKeyChecking no" >> /home/appuser/.ssh/config
RUN echo "export COLUMNS=300" >> /home/appuser/.bashrc
RUN echo "alias sf=/appdata/www/bin/console" >> /home/appuser/.bashrc

# Copiar el archivo de configuración php.ini
COPY ./docker/php/php.ini /usr/local/etc/php/php.ini

# Instalar dependencias necesarias y extensiones de PHP
RUN apt-get update \
    && apt-get install -y git acl openssl openssh-client wget zip vim libssh-dev \
    && apt-get install -y libpng-dev zlib1g-dev libzip-dev libxml2-dev libicu-dev \
    && docker-php-ext-install intl pdo pdo_mysql zip gd soap bcmath sockets \
    && pecl install xdebug \
    && docker-php-ext-enable --ini-name 05-opcache.ini opcache xdebug

# Instalar Composer
RUN curl https://getcomposer.org/composer.phar -o /usr/bin/composer && chmod +x /usr/bin/composer
RUN composer self-update

# Instalar Symfony y otras herramientas
RUN wget https://cs.symfony.com/download/php-cs-fixer-v2.phar -O php-cs-fixer
RUN chmod a+x php-cs-fixer
RUN mv php-cs-fixer /usr/local/bin/php-cs-fixer

# Crear el directorio de la aplicación
RUN mkdir -p /appdata/www

# Establecer el directorio de trabajo
WORKDIR /appdata/www

# Copiar los archivos de Composer y Symfony desde la raíz del proyecto
COPY ./composer.json ./composer.lock ./
RUN composer install --prefer-dist --no-dev --no-scripts --no-progress --no-suggest

# Copiar el resto de los archivos de la aplicación
COPY . .

# Ejecutar los comandos de Symfony después de copiar los archivos
RUN composer dump-autoload --optimize
RUN php bin/console cache:clear --env=prod

# Cambiar la propiedad de los archivos a appuser
RUN chown -R appuser:appuser /appdata/www

# Comando por defecto al iniciar el contenedor
CMD ["php-fpm"]
