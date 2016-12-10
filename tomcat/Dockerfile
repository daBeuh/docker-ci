FROM oojava:1.8.0

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME

ENV TOMCAT_MAJOR 8
ENV TOMCAT_VERSION 8.0.39

ENV TOMCAT_TGZ_URL https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz

RUN yum -y update && \
 yum -y install wget && \
 yum -y install tar && \
 yum clean all

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

# Create Tomcat user
RUN groupadd -r tomcat \
	&& useradd -g tomcat -d ${CATALINA_HOME} -s /sbin/nologin -c "Tomcat user" tomcat \
	&& chown -R tomcat:tomcat ${CATALINA_HOME} \
 	&& chmod -R 400 ${CATALINA_HOME}/conf/* \
 	&& chmod -R 300 ${CATALINA_HOME}/logs

USER tomcat

EXPOSE 8080
CMD ["catalina.sh", "run"]