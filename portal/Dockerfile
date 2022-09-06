FROM node:18-alpine3.15 as build

RUN npm install -g @angular/cli
ADD /package.json /app/package.json

WORKDIR /app/
RUN npm install 
ADD . /app/
RUN ng build

FROM nginx:1.23.1-alpine

COPY --from=build /app/dist/portal /usr/share/nginx/html
