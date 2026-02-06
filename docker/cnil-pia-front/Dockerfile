# ===== Étape 1 : Build Angular avec Node 20 =====
FROM node:20 AS build

# Optionnel : pour des installations plus rapides en CI
ENV CI=true

WORKDIR /app

# Récupère le code du front depuis le dépôt officiel LINCnil
# (Option A) Toujours la dernière version de la branche par défaut
RUN git clone --depth 1 https://github.com/LINCnil/pia.git .

# (Option B) Si tu veux pinner une release précise au lieu de la branche :
# ARG PIA_VERSION=3.2.2
# RUN wget -O pia.zip "https://github.com/LINCnil/pia/archive/refs/tags/v${PIA_VERSION}.zip" \
#  && unzip pia.zip && rm pia.zip \
#  && mv "pia-${PIA_VERSION}"/* . \
#  && rm -rf "pia-${PIA_VERSION}"

# Active Corepack et choisit Yarn (projet PIA utilise Yarn)
RUN corepack enable && corepack prepare yarn@stable --activate

# Installe les dépendances (le projet fournit un lockfile)
RUN yarn install --frozen-lockfile

# Build de prod (adapte si besoin selon package.json)
# - Soit "yarn build"
# - Soit "yarn ng build --configuration production"
RUN yarn build

# ===== Étape 2 : Runtime nginx =====
FROM nginx:alpine

# (Optionnel) Environnement d'exécution
ENV TZ=Europe/Paris

# Copie du site statique construit par Angular
# NB: Ajuste le chemin selon la sortie réelle du build :
#     souvent /app/dist/pia ou /app/dist/<projectName>
COPY --from=build /app/dist/ /usr/share/nginx/html/

# (Optionnel) Si tu as un fichier de conf nginx custom
# COPY ./conf/cnil_pia.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
