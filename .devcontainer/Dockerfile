# We'll base our dev container image on Alpine Linux.
FROM mcr.microsoft.com/vscode/devcontainers/base:alpine

# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"
# [Option] Enable non-root Docker access in container
ARG ENABLE_NONROOT_DOCKER="true"

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG SOURCE_SOCKET=/var/run/docker-host.sock
ARG TARGET_SOCKET=/var/run/docker.sock
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
COPY script/*.sh /tmp/library-scripts/
RUN apk update \
    && /bin/sh /tmp/library-scripts/common-alpine.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" \
    # Clean up
    && rm -rf /var/cache/apk/* /tmp/library-scripts/

# Then we'll add the dependencies we need:
# - git and gnupg are useful for remote development so that we
#   can commit and sign commits from within the container
# - and of course nginx itself

RUN apk add --no-cache \
  git \
  gnupg \
  nginx

# RUN mkdir -p /etc/nginx/conf.d
COPY nginx/conf.d/default.conf /etc/nginx/http.d/default.conf

# Finally, we'll remove nginx's default content and replace it
# with a symlink to our source code.
RUN rm -rf /usr/share/nginx/html
RUN ln -s /workspaces/week-01/lab /usr/share/nginx/html

# Expose port 80 to the host
EXPOSE 80

# Set nginx as the entrypoint to start it automatically
ENTRYPOINT ["nginx", "-g", "daemon off;"]