FROM ubuntu
RUN apt-get update \
    && apt-get install -y apt-utils \
    && apt-get install -y locales \
    && apt-get install -y curl \
    && apt-get install -y gnupg2 \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i es_ES -c -f UTF-8 -A /usr/share/locale/locale.alias es_ES.UTF-8
ENV LANG es_ES.utf8
RUN curl -sL https://deb.nodesource.com/setup_6.x -o nodesource_setup.sh \
    && chmod +x nodesource_setup.sh \
    && ./nodesource_setup.sh \
    && apt-get install -y nodejs \
    && apt-get install -y npm \
    && apt-get install -y build-essential 
WORKDIR /usr/src/app
COPY package*.json server.js ./
RUN npm install
EXPOSE 8080
CMD [ "npm", "start" ]
