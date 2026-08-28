# Build stage
FROM node:18.18-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm install --force

COPY . .

RUN npm run build

# Run stage
FROM nginx:alpine

COPY --from=build /app/dist/angular-ecommerce /usr/share/nginx/html

COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
