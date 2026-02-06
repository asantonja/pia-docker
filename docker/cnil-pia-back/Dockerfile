FROM ruby:3.4-slim AS base

ENV DEBIAN_FRONTEND=noninteractive \
    RAILS_ENV=production \
    BUNDLE_DEPLOYMENT=1 \
    BUNDLE_WITHOUT="development test" \
    BUNDLE_JOBS=4 \
    BUNDLE_RETRY=3

# Dépendances système
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential git curl ca-certificates \
    libpq-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Récupération du code backend officiel
RUN git clone --depth 1 https://github.com/LINCnil/pia-back.git .

# SUPPRESSION du lock d’origine (il est incompatible Linux)
RUN rm -f Gemfile.lock

# Regeneration du Gemfile.lock nativement Linux
RUN bundle lock --add-platform x86_64-linux || true

# Installation des gems
RUN bundle install --no-cache

EXPOSE 3000

CMD ["bin/rails", "server", "-b", "0.0.0.0"]
