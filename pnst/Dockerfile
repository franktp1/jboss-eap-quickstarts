FROM jboss-eap-7/eap72-openshift
LABEL io.k8s.description="kitchensink"

USER root

# Make directories. For now named after the Weblogic names.
RUN mkdir -p /opt/Tivoli/monitoring/logs && \
    mkdir -p /data/weblogic/pnst/logs && \
	mkdir -p /weblogic && \
	chmod a+w /data/weblogic/pnst/logs && \
	chmod a+w /weblogic


USER jboss

# copy the activemq-rar-5.11.0.redhat-630187.rar
# note; there was one already !?!  unknown version
#RUN  curl -L -X GET 'http://nexus3.tools.svc.cluster.local:8081/service/rest/v1/search/assets/download?sort=version&repository=maven-releases&group=activemq.lib&name=mqrar&maven.baseVersion=5.11&maven.extension=jar' \
#  --output /opt/eap/standalone/deployments/activemq-rar.rar

# Create the Postgres jdbc driver
COPY pnst/module.xml /opt/eap/modules/system/layers/base/org/postgresql/main/module.xml
RUN ls -ltr /opt/eap/modules/system/layers/base/org/postgresql/main/*
RUN  curl -L -X GET 'http://nexus3.tools.svc.cluster.local:8081/service/rest/v1/search/assets/download?sort=version&repository=maven-releases&group=org.database.driver&name=postgresql&maven.baseVersion=0.1&maven.extension=jar' \
  --output /opt/eap/modules/system/layers/base/org/postgresql/main/postgresql-42.2.16.jar


# copy the configuation file
COPY pnst/standalone-full.xml /opt/eap/standalone/configuration/
COPY pnst/standalone-full.xml /opt/eap/standalone/configuration/standalone.xml
COPY pnst/standalone-full.xml /opt/eap/standalone/configuration/standalone-openshift.xml


# option 1: get it from the shared workspace
# TODO

# option 2: get it from nexus
#RUN  curl -L -X GET 'http://nexus3.tools.svc.cluster.local:8081/service/rest/v1/search/assets/download?sort=version&repository=maven-snapshots&group=org.jboss.eap.quickstarts&name=pnst&maven.baseVersion=0.1-SNAPSHOT&maven.extension=ear' \
#  --output /opt/eap/standalone/deployments/ROOT.war

# note: we can upload releases but not snapshots.
RUN  curl -L -X GET 'http://nexus3.tools.svc.cluster.local:8081/service/rest/v1/search/assets/download?sort=version&repository=maven-releases&group=org.jboss.eap.quickstarts&name=pnst&maven.baseVersion=0.1&maven.extension=ear' \
  --output /opt/eap/standalone/deployments/ROOT.ear


# Add admin console user
RUN /opt/eap/bin/add-user.sh admin Admin#70365 --silent
RUN /opt/eap/bin/add-user.sh -a -r ApplicationRealm -g guest -u jms -p test4all --silent:q


ENV DEP_DIR="/opt/eap/standalone/deployments"
RUN chown -R jboss:0 $DEP_DIR 


EXPOSE 8080
EXPOSE 9990
