### docker build --pull -t acme/starter-api -t acme/starter-api:v3.2 .
FROM registry.access.redhat.com/rhel7
MAINTAINER Red Hat Systems Engineering <refarch-feedback@redhat.com>

### Atomic/OpenShift Labels - https://github.com/projectatomic/ContainerApplicationGenericLabels
LABEL name="acme/starter-api" \
      vendor="Acme Corp" \
      version="3.2" \
      release="1" \
### Recommended labels below
      build-date="2016-10-12T14:12:54.553894Z" \
      url="https://www.acme.io" \
      summary="Acme Corp's starter-api app" \
      description="starter-api app will do ....." \
      run='docker run -tdi --name ${NAME} ${IMAGE}' \
      io.k8s.description="starter-api app will do ....." \
      io.k8s.display-name="starter-api app" \
      io.openshift.expose-services="" \
      io.openshift.tags="acme,starter-api,starter,api"

### Atomic Help File - Write in Markdown, it will be converted to man format at build time.
### https://github.com/projectatomic/container-best-practices/blob/master/creating/help.adoc
COPY help.md user_setup /tmp/

RUN yum clean all && yum-config-manager --disable \* &> /dev/null && \
### Add necessary Red Hat repos here
    yum-config-manager --enable rhel-7-server-rpms,rhel-7-server-optional-rpms &> /dev/null && \
    yum -y update-minimal --security --sec-severity=Important --sec-severity=Critical --setopt=tsflags=nodocs && \
### Add your package needs to this installation line
    yum -y install --setopt=tsflags=nodocs golang-github-cpuguy83-go-md2man iputils bind-utils && \
### help file markdown to man conversion
    go-md2man -in /tmp/help.md -out /help.1 && yum -y remove golang-github-cpuguy83-go-md2man && \
### EPEL needs
    curl -o epel-release-latest-7.noarch.rpm -SL https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
            --retry 9 --retry-max-time 0 -C - && \
    rpm -ivh epel-release-latest-7.noarch.rpm && rm epel-release-latest-7.noarch.rpm && \
    yum -y install --setopt=tsflags=nodocs jq && \
    yum-config-manager --disable epel && \
    yum clean all

### Setup user for build execution and application runtime
ENV APP_ROOT=/opt/app-root \
    USER_NAME=default \
    USER_UID=10001
ENV APP_HOME=${APP_ROOT}/src  PATH=$PATH:${APP_ROOT}/bin
RUN mkdir -p ${APP_HOME}
COPY bin/ ${APP_ROOT}/bin/
RUN chmod -R ug+x ${APP_ROOT}/bin /tmp/user_setup && \
    /tmp/user_setup

####### Add app-specific needs below. #######
### Containers should NOT run as root as a good practice
USER ${USER_UID}
WORKDIR ${APP_ROOT}
CMD run