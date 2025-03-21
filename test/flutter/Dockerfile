#--------------------------------------
# Image: base
#--------------------------------------
FROM ubuntu:focal as base

RUN touch /.dummy

ARG APT_HTTP_PROXY
ARG CONTAINERBASE_DEBUG

ENV BASH_ENV=/usr/local/etc/env
SHELL ["/bin/bash" , "-c"]

COPY src/ /

RUN install-containerbase

# TODO: move to prepare
# renovate: datasource=github-tags packageName=git/git
RUN install-tool git v2.43.0

COPY --chown=1000:0 test/flutter/test test
WORKDIR /test

#--------------------------------------
# build
#--------------------------------------
FROM base as build

RUN prepare-tool flutter

#--------------------------------------
# test: flutter 1.x (root)
#--------------------------------------
FROM build as testa

# EOL, use source, not prebuild
RUN install-tool flutter 1.22.5

USER 1000

RUN set -ex; \
    ls -la /opt/containerbase/tools/flutter/1.22.5 $USER_HOME; \
    cd a; \
    flutter pub upgrade;

RUN set -ex; \
    cd b; \
    flutter pub upgrade;

#--------------------------------------
# test: flutter 1.x (non-root)
#--------------------------------------
FROM build as testb

USER 1000

# EOL
RUN install-tool flutter 1.22.6

RUN set -ex; \
    ls -la /opt/containerbase/tools/flutter/1.22.6; \
    cd a; \
    flutter pub upgrade;

RUN set -ex; \
    cd b; \
    flutter pub upgrade;

#--------------------------------------
# test: flutter 2.x (non-root)
#--------------------------------------
FROM build as testc

USER 1000

# EOL
RUN install-tool flutter 2.10.5

RUN set -ex; \
    cd c; \
    flutter pub upgrade;

#--------------------------------------
# test: flutter 3.x (non-root)
#--------------------------------------
FROM build as testd

USER 1000

# renovate: datasource=flutter-version
RUN install-tool flutter 3.16.5

RUN set -ex; \
    cd d; \
    flutter pub upgrade;

#--------------------------------------
# test: flutter 3.x (non-root, openshift)
#--------------------------------------
FROM build as teste

# fix test file perms for openstack
RUN chown -R 1001 .

USER 1001

# renovate: datasource=flutter-version
RUN install-tool flutter 3.16.5

RUN set -ex; \
    cd d; \
    flutter pub upgrade;

#--------------------------------------
# final
#--------------------------------------
FROM base

COPY --from=testa /.dummy /.dummy
COPY --from=testb /.dummy /.dummy
COPY --from=testc /.dummy /.dummy
COPY --from=testd /.dummy /.dummy
COPY --from=teste /.dummy /.dummy
