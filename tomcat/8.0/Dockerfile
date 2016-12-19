FROM oojava:1.8.0

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME

RUN yum -y update \
	&& yum -y install wget \
 	&& yum -y install tar \
 	&& yum clean all

ARG TOMCAT_VERSION
ENV TOMCAT_MAJOR 8
ENV TOMCAT_VERSION ${TOMCAT_VERSION:-8.0.20}

ENV TOMCAT_TGZ_URL https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz

RUN set -x \
	\
	&& wget -O tomcat.tar.gz "$TOMCAT_TGZ_URL" \
	&& wget -O tomcat.tar.gz.asc "$TOMCAT_ASC_URL" \
	&& tar -xvf tomcat.tar.gz --strip-components=1 \
	&& rm bin/*.bat \
	&& rm -rf webapps/* \
	&& rm tomcat.tar.gz* \
	&& rm bin/tomcat-native.tar.gz \
	\
	&& ( \
		export CATALINA_HOME="$PWD" \
	)

ENV NEXUS_URL http://nexus.oodrive.net:8081/nexus/service/local/repositories
ARG POSTGRES_DRIVER_VERSION
ENV POSTGRES_DRIVER_VERSION ${POSTGRES_DRIVER_VERSION:-9.2-1004-jdbc41}
ARG CORRELATION_VALVE_VERSION
ENV CORRELATION_VALVE_VERSION ${CORRELATION_VALVE_VERSION:-1.0.2}

RUN \
	wget -q -O lib/postgresql.jar $NEXUS_URL/central/content/org/postgresql/postgresql/$POSTGRES_DRIVER_VERSION/postgresql-$POSTGRES_DRIVER_VERSION.jar \
	&& wget -q -O lib/catalina-jmx-remote.jar https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/extras/catalina-jmx-remote.jar \
	&& wget -q -O lib/correlation-valve.jar $NEXUS_URL/releases/content/com/oodrive/logging/correlation/correlation-valve/$CORRELATION_VALVE_VERSION/correlation-valve-$CORRELATION_VALVE_VERSION.jar

# Create Tomcat user
RUN groupadd -r tomcat \
	&& useradd -g tomcat -d ${CATALINA_HOME} -s /sbin/nologin -c "Tomcat user" tomcat \
	&& chown -R tomcat:tomcat ${CATALINA_HOME} \
 	&& chmod -R 400 ${CATALINA_HOME}/conf/* \
 	&& chmod -R 300 ${CATALINA_HOME}/logs

COPY wait-for-it.sh /
RUN chmod +x /wait-for-it.sh

USER tomcat

EXPOSE 8080
CMD ["catalina.sh", "run"]