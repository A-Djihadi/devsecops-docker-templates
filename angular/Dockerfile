# syntax=docker/dockerfile:1.7

FROM node:20-alpine AS build

WORKDIR /workspace

COPY package.json package-lock.json ./

RUN --mount=type=cache,target=/root/.npm \
    npm ci --no-audit --no-fund

COPY . .

ARG BUILD_CONFIGURATION=production
RUN npm run build -- --configuration=${BUILD_CONFIGURATION}

FROM nginx:1.27-alpine AS runtime

LABEL org.opencontainers.image.title="angular-template" \
      org.opencontainers.image.description="Secure multi-stage Angular Docker template"

ARG BUILD_OUTPUT=dist/app/browser

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /workspace/${BUILD_OUTPUT}/ /usr/share/nginx/html/

RUN chown -R nginx:nginx /usr/share/nginx/html /var/cache/nginx /var/run /var/log/nginx

USER nginx

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD wget -qO- http://127.0.0.1:8080/healthz || exit 1

CMD ["nginx", "-g", "daemon off;"]
