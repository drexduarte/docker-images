# LICENSE CDDL 1.0 + GPL 2.0
#
# ORACLE DOCKERFILES PROJECT
# --------------------------
# This Dockerfile extends an Oracle WebLogic image by creating a sample domain.
# Util scripts are copied into the image enabling users to plug NodeManager 
# magically into the AdminServer running on another container as a Machine.
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Put all downloaded files in the same directory as this Dockerfile
# Run: 
#      $ sudo docker build -t myweblogic .
#

# Pull base image
# ---------------
FROM oracle/weblogic:10.3.6-developer

# Maintainer
# ----------
MAINTAINER Andre Duarte <drexduarte@gmail.com>

# Domain and Server environment variables
# ------------------------------------------------------------
ENV ADMIN_PORT="${ADMIN_PORT:-7001}"  \
    ADMIN_USERNAME="${ADMIN_USERNAME:-weblogic}" \
    ADMIN_PASSWORD="${ADMIN_PASSWORD:-"weblogic#1"}" \
	ADMIN_PORT_SSL="${ADMIN_PORT_SSL:-7002}" \
	NM_PORT="${NM_PORT:-5556}"

# Add files required to build this image
COPY container-scripts/* /u01/oracle/

# Root commands
USER root
RUN echo ". /u01/oracle/weblogic/user_projects/domains/base_domain/bin/setDomainEnv.sh" >> /root/.bashrc && \
    echo "export PATH=$PATH:/u01/oracle/weblogic/wlserver/server/bin:/u01/oracle/weblogic/wlserver/common/bin:/u01/oracle/weblogic/user_projects/domains/base_domain/bin" >> /root/.bashrc

# Configuration of WLS Domain
USER oracle
WORKDIR /u01/oracle/weblogic
RUN /u01/oracle/weblogic/wlserver/common/bin/wlst.sh -skipWLSModuleScanning /u01/oracle/create-wls-domain.py && \
    mkdir -p /u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/security && \
    echo "username=$ADMIN_USERNAME" > /u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/security/boot.properties && \ 
    echo "password=$ADMIN_PASSWORD" >> /u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/security/boot.properties && \
    echo ". /u01/oracle/weblogic/user_projects/domains/base_domain/bin/setDomainEnv.sh" >> /u01/oracle/.bashrc && \ 
    echo "export PATH=$PATH:/u01/oracle/weblogic/wlserver/server/bin:/u01/oracle/weblogic/wlserver/common/bin:/u01/oracle/weblogic/user_projects/domains/base_domain/bin" >> /u01/oracle/.bashrc && \
    rm /u01/oracle/create-wls-domain.py

# Expose Node Manager default port, and also default http/https ports for admin console
EXPOSE $NM_PORT $ADMIN_PORT $ADMIN_PORT_SSL

USER root
RUN chmod -R a+rx /u01/oracle && \
	chmod -R a+w /u01/oracle/weblogic/user_projects/domains/base_domain && \
	chown -R oracle:root /u01/oracle

# Final setup
USER oracle
WORKDIR /u01/oracle/weblogic/user_projects/domains/base_domain

ENV PATH $PATH:/u01/oracle/weblogic/wlserver/server/bin:/u01/oracle/weblogic/wlserver/common/bin:/u01/oracle/weblogic/user_projects/domains/base_domain/bin:/u01/oracle

# Define default command to start bash. 
USER oracle
CMD ["startWebLogic.sh"]
