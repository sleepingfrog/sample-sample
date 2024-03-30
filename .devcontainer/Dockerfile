# syntax=docker/dockerfile:1

ARG RUBY_VERSION
ARG DISTRO_NAME=bullseye
FROM ruby:$RUBY_VERSION-slim-bullseye

# Common dependencies
# Using --mount to speed up build with caching, see https://github.com/moby/buildkit/blob/master/frontend/dockerfile/docs/reference.md#run---mount
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
  --mount=type=cache,target=/var/lib/apt,sharing=locked \
  --mount=type=tmpfs,target=/var/log \
  rm -f /etc/apt/apt.conf.d/docker-clean; \
  echo 'Binary::apt::APT::Keep-Downloaded-Packages "true";' > /etc/apt/apt.conf.d/keep-cache; \
  apt-get update -qq && \
  DEBIAN_FRONTEND=noninteractive apt-get -yq dist-upgrade && \
  DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
    build-essential \
    gnupg2 \
    curl \
    less \
    git

# Application dependencies
# We use an external Aptfile for this, stay tuned
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    --mount=type=tmpfs,target=/var/log \
    --mount=type=bind,source=Aptfile,target=/tmp/Aptfile \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
      $(grep -Ev '^\s*#' /tmp/Aptfile | xargs)

# Configure bundler
ENV LANG=C.UTF-8 \
  BUNDLE_JOBS=4 \
  BUNDLE_RETRY=3

# Store Bundler settings in the project's root
ENV BUNDLE_APP_CONFIG=.bundle

# Upgrade RubyGems and install the latest Bundler version
RUN gem update --system && \
    gem install bundler

# Create a directory for the app code
RUN mkdir -p /app/vendor/bundle
RUN groupadd -g 1000 nonroot && \
  useradd -u 1000 -g nonroot -m nonroot

RUN chown -R nonroot /app/vendor/bundle

WORKDIR /app
USER nonroot
EXPOSE 3000
CMD ["/bin/bash"]
