# docker/cnil-pia-back/Dockerfile

# 1) Base Ruby alignée sur PIA-BACK actuel (Ruby 3.4.x)
FROM ruby:3.4-slim

# 2) Dépendances système pour Rails + gem 'pg'
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential git curl ca-certificates \
    libpq-dev pkg-config \
 && rm -rf /var/lib/apt/lists/*

# 3) Bundler récent (compatible Rails 8)
RUN gem update --system && gem install bundler -v "~>2.5"

# 4) Variables d'environnement (prod) et Bundler
ENV DEBIAN_FRONTEND=noninteractive \
    RAILS_ENV=production \
    RAILS_SERVE_STATIC_FILES=true \
    BUNDLE_WITHOUT="development test"

# 5) Code application : récupération du backend officiel
#    Option A : toujours la dernière branche
WORKDIR /var/www
RUN git clone --depth 1 https://github.com/LINCnil/pia-back.git .

#    Option B : pinner une release connue
# ARG PIA_VERSION=3.2.2
# RUN wget -O pia-back.zip "https://github.com/LINCnil/pia-back/archive/refs/tags/v${PIA_VERSION}.zip" \
#  && unzip pia-back.zip && rm pia-back.zip \
#  && mv "pia-back-${PIA_VERSION}"/* . && rm -rf "pia-back-${PIA_VERSION}"

# 6) Préparer le lock pour Linux puis installer les gems
#    (évite les soucis de plateforme en build Docker)
RUN bundle lock --add-platform x86_64-linux \
 && bundle install --jobs=4 --retry=3

# 7) Port d’écoute puma
EXPOSE 3000

# 8) Démarrage
CMD ["bin/rails", "server", "-b", "0.0.0.0", "-p", "3000"]
