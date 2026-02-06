FROM ruby:3.4-slim

# Dépendances système
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential git curl ca-certificates \
    libpq-dev pkg-config \
 && rm -rf /var/lib/apt/lists/*

# Bundler récent
RUN gem update --system && gem install bundler -v "~>2.5"

ENV DEBIAN_FRONTEND=noninteractive \
    RAILS_ENV=production \
    RAILS_SERVE_STATIC_FILES=true \
    BUNDLE_WITHOUT="development test"

WORKDIR /var/www

# --- 1. Cloner le backend PIA ---
RUN git clone --depth 1 https://github.com/LINCnil/pia-back.git .

# --- 2. SUPPRIMER le Gemfile.lock fourni par LINCnil ---
#     (car il ne contient pas la plateforme Linux)
RUN rm -f Gemfile.lock

# --- 3. REGENERATION du lock POUR LINUX ---
RUN bundle lock --add-platform x86_64-linux

# --- 4. Installation des gems ---
RUN bundle install --jobs=4 --retry=3

EXPOSE 3000
CMD ["bin/rails", "server", "-b", "0.0.0.0", "-p", "3000"]
