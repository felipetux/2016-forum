# Use jbossdemocentral/developer as the base
FROM jbossdemocentral/developer

# Maintainer details
MAINTAINER Andrew Block, Eric D. Schabell

# Environment Variables 
ENV BRMS_HOME /opt/jboss/brms/jboss-eap-6.4
ENV BRMS_VERSION_MAJOR 6
ENV BRMS_VERSION_MINOR 3
ENV BRMS_VERSION_MICRO 0
ENV BRMS_VERSION_PATCH GA

ENV EAP_VERSION_MAJOR 6
ENV EAP_VERSION_MINOR 4
ENV EAP_VERSION_MICRO 0
ENV EAP_VERSION_PATCH 7

RUN cat /etc/resolv.conf

# ADD Installation Files
COPY support/installation-brms support/installation-eap support/installation-brms.variables support/installation-eap.variables installs/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_PATCH-installer.jar installs/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar installs/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_PATCH-patch.zip /opt/jboss/

# Update Permissions on Installers
USER root
RUN chown 1000:1000 /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar /opt/jboss/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_PATCH-installer.jar 
USER 1000

# Prepare and run installer and cleanup installation components
RUN sed -i "s:<installpath>.*</installpath>:<installpath>$BRMS_HOME</installpath>:" /opt/jboss/installation-eap \
    && sed -i "s:<installpath>.*</installpath>:<installpath>$BRMS_HOME</installpath>:" /opt/jboss/installation-brms \
	&& java -jar /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar  /opt/jboss/installation-eap -variablefile /opt/jboss/installation-eap.variables \
    && $BRMS_HOME/bin/jboss-cli.sh --command="patch apply /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_PATCH-patch.zip --override-all" \
    && java -jar /opt/jboss/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_PATCH-installer.jar  /opt/jboss/installation-brms -variablefile /opt/jboss/installation-brms.variables \
    && rm -rf /opt/jboss/jboss-brms-$BRMS_VERSION_MAJOR.$BRMS_VERSION_MINOR.$BRMS_VERSION_MICRO.$BRMS_VERSION_PATCH-installer.jar /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO-installer.jar /opt/jboss/jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_PATCH-patch.zip /opt/jboss/installation-brms /opt/jboss/installation-brms.variables /opt/jboss/installation-eap /opt/jboss/installation-eap.variables $BRMS_HOME/standalone/configuration/standalone_xml_history/ \
   && git clone http://${GOGS_URL}/${GOGS_USERNAME}/brms.git --bare /opt/jboss/brms/jboss-eap-6.4/bin/.niogit/brms.git \
   && cd /opt/jboss/brms/jboss-eap-6.4/bin/.niogit/brms.git \
   && git remote set-url origin http://${GOGS_USERNAME}:${GOGS_PASSWORD}@${GOGS_URL}/${GOGS_USERNAME}/brms.git \
   && echo $'#!/bin/sh\ngit push origin master' > /opt/jboss/brms/jboss-eap-6.4/bin/.niogit/brms.git/hooks/post-commit \
   && chmod u+x /opt/jboss/brms/jboss-eap-6.4/bin/.niogit/brms.git/hooks/post-commit \
### ADDED BY MALTRON: This it will run everytime JBoss starts
   && sed -i '2isource /tmp/config.sh' /opt/jboss/brms/jboss-eap-6.4/bin/standalone.sh

# Copy demo and support files
COPY support/userinfo.properties $BRMS_HOME/standalone/deployments/business-central.war/WEB-INF/classes/
COPY support/application-roles.properties support/standalone.xml $BRMS_HOME/standalone/configuration/

# Swtich back to root user to perform build and cleanup
USER root

# Run Demo Maven build and cleanup
RUN chown -R jboss:jboss $BRMS_HOME/standalone/configuration/application-roles.properties $BRMS_HOME/standalone/configuration/standalone.xml

# Run as JBoss 
USER 1000


# Expose Ports
EXPOSE 9990 9999 8080

# Run BRMS
CMD ["/opt/jboss/brms/jboss-eap-6.4/bin/standalone.sh","-c","standalone.xml","-b", "0.0.0.0","-bmanagement","0.0.0.0","-Djboss.as.management.blocking.timeout=3600"]
