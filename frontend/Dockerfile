# Etape 1 : build Angular
FROM node:22 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Etape 2 : serveur nginx
FROM nginx:alpine
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /tmp/dist
RUN cp -r /tmp/dist/*/browser/. /usr/share/nginx/html/ && rm -rf /tmp/dist
EXPOSE 80
