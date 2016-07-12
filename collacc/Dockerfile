FROM debian:jessie


# set up Apache 2.2 + PHP 5.3, Unzip, ImageMagick ou GraphicsMagick, libGD, ffmpeg,
## qt-faststart, Ghostscript, dcraw, PdfToText, AbiWord, MediaInfo, LibreOffice
## WkHTMLToPDF, ExifTool, PDFMiner
RUN echo "deb http://ftp.debian.org/debian jessie-backports main" >> /etc/sources.list

RUN apt-get update \
  && apt-get install -y apache2 php5 php5-mysql unzip wget \
  imagemagick dcraw graphicsmagick mediainfo libreoffice \
  python-pdfminer ghostscript wkhtmltopdf \
  libimage-exiftool-perl libgd3 libav-tools poppler-utils \
  php5-curl \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# apt-get clean & rm -rf /var/lib/apt/lists/* --> libere espace dans conteneur après apt-get update
# RUN apt-get install -y abiword # command returned a non-zero code: 100 : Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?

# edit file php.ini  
# RUN open /etc/php.ini or /usr/local/lib/php.ini
  ## && post_max_size = 100
  ## && upload_max_filesize = 100
  ## && memory_limit = 200
  ## && display_errors = ON
### --> fait manuellement


# fichier caches non pris en compte
# ajoute groupe webdev
# tous les fichiers dans www/html appartiennent à root et webdev
# changement des droits --> 775 = root et webdev --> tous les droits, tout le monde --> lecture seule
# droits par défaut pour tous les dossiers
# droits par défaut pour tous les fichiers root et webdev --> droits wr - et non wrx, tout le monde --> droits r

RUN wget https://github.com/collectiveaccess/providence/archive/master.zip \
  && unzip master.zip \
  && rm master.zip \
  && mv providence-master/* /var/www/html/ \
  && mv providence-master/.htaccess /var/www/html/ \
  && rm /var/www/html/index.html \
  && groupadd webdev \
  && chown -R root.webdev /var/www/html \
  && chmod 775 /var/www/html \
  && find /var/www/html -type d -print0 | xargs -0 chmod 775 \
  && chmod 777 /var/www/html/app/tmp \
  && find /var/www/html -type f -print0 | xargs -0 chmod 664 \
  && chmod 777 /var/www/html/vendor/ezyang/htmlpurifier/library/HTMLPurifier/DefinitionCache \
  && chmod 777 /var/www/html/vendor/ezyang/htmlpurifier/library/HTMLPurifier/DefinitionCache/Serializer \
  && chmod 777 /var/www/html/media

# --> print0 insere caractere nul entre chaque nom de dossier/fichier et xargs = applique a chaque ligne (donc nom) la commande suivante. xargs -0 : utilise comme separateur le caractere nul 

COPY php.ini /etc/php5/cli/php.ini
COPY setup.php /var/www/html/setup.php

EXPOSE 80

CMD service apache2 start && tail -F /var/www/html/application/logs/errors.log
