FROM tomcat:8.5-jre8

ENV GN_FILE geonetwork.war
ENV DATA_DIR=$CATALINA_HOME/webapps/geonetwork/WEB-INF/data
ENV JAVA_OPTS="-Djava.security.egd=file:/dev/./urandom -Djava.awt.headless=true -server -Xms512m -Xmx2024m -XX:NewSize=512m -XX:MaxNewSize=1024m -XX:+UseConcMarkSweepGC"

#Environment variables
ENV GN_VERSION 3.6.0
ENV GN_DOWNLOAD_MD5 06601ea4b16e4f8e806c7369ea0060ae

WORKDIR $CATALINA_HOME/webapps

RUN curl -fSL -o $GN_FILE \
     https://sourceforge.net/projects/geonetwork/files/GeoNetwork_opensource/v${GN_VERSION}/geonetwork.war/download && \
     echo "$GN_DOWNLOAD_MD5 *$GN_FILE" | md5sum -c && \
     mkdir -p geonetwork && \
     unzip -e $GN_FILE -d geonetwork && \
     rm $GN_FILE

#Set PostgreSQL as default GN DB
RUN sed -i -e 's#<import resource="../config-db/h2.xml"/>#<!--<import resource="../config-db/h2.xml"/> -->#g' $CATALINA_HOME/webapps/geonetwork/WEB-INF/config-node/srv.xml && \
sed -i -e 's#<!--<import resource="../config-db/postgres.xml"/>-->#<import resource="../config-db/postgres.xml"/>#g' $CATALINA_HOME/webapps/geonetwork/WEB-INF/config-node/srv.xml && \
sed -i -e "s#'eng': 'en',#'eng': 'en',\n 'por': 'pt',#g" $CATALINA_HOME/webapps/geonetwork/catalog/js/CatController.js &&\
sed -i -e "s#$scope.langLabels = {#$scope.langLabels = {'por':'Português', #g" $CATALINA_HOME/webapps/geonetwork/catalog/js/CatController.js




COPY ./jdbc.properties $CATALINA_HOME/webapps/geonetwork/WEB-INF/config-db/jdbc.properties

RUN apt-get update && apt-get install -y postgresql-client && \
    rm -rf /var/lib/apt/lists/*

#Initializing database & connection string for GN
COPY locales/ $CATALINA_HOME/webapps/geonetwork/catalog/locales
COPY ./labels.xml $CATALINA_HOME/webapps/geonetwork/WEB-INF/data/config/schema_plugins/iso19139/loc/por/labels.xml
COPY ./codelists.xml $CATALINA_HOME/webapps/geonetwork/WEB-INF/data/config/schema_plugins/iso19139/loc/por/codelists.xml
COPY ./docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

CMD ["catalina.sh", "run"]


