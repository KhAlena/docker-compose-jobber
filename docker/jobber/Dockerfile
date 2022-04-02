FROM yobasystems/alpine:3.14.0-amd64
USER root

# Install jobber
ENV JOBBER_VERSION 1.4.4
ENV JOBBER_SHA256 ec09b2efafff69c91fba2124bf28607209e1c9b50ed834ff444a9d40798aa8d3
RUN wget -O /tmp/jobber.apk "https://github.com/dshearer/jobber/releases/download/v${JOBBER_VERSION}/jobber-${JOBBER_VERSION}-r0.apk" && \
    echo -n "Actual checksum: " && sha256sum /tmp/jobber.apk && \
    echo "${JOBBER_SHA256} */tmp/jobber.apk" | sha256sum -c && \
# --no-scripts is needed b/c the post-install scripts don't work in Docker
    apk add --no-network --no-scripts --allow-untrusted /tmp/jobber.apk && \
    rm /tmp/jobber.apk

# Install Docker and dependencies
RUN apk --update add \
  bash \
  iptables \
  ca-certificates \
  e2fsprogs \
  docker \
  && rm -rf /var/cache/apk/*
RUN apk add --no-cache docker openrc
RUN rc-update add docker boot

# Prepare jobber
RUN mkdir /jobber
ENV USERID 0
RUN mkdir -p "/var/jobber/${USERID}"

# Prepare jobfile
COPY jobfile /jobs/.jobber
RUN chmod -R 0600 /jobs/

# Copy entrypoint and set permissions
COPY docker-entrypoint.sh /
RUN chmod 777 /docker-entrypoint.sh

# Start docker and run jobber daemon
CMD ["/docker-entrypoint.sh"]
