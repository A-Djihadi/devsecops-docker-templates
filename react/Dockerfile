# syntax=docker/dockerfile:1

FROM node:20-alpine AS build

WORKDIR /workspace

COPY package.json package-lock.json ./

RUN --mount=type=cache,target=/root/.npm \
    npm ci --no-audit --no-fund

COPY . .

ARG BUILD_ENV=production
ENV NODE_ENV=${BUILD_ENV}

RUN npm run build

FROM nginx:1.27-alpine AS runtime

LABEL org.opencontainers.image.title="react-template" \
      org.opencontainers.image.description="Secure multi-stage React Docker template"

ARG APP_UID=1000
ARG APP_GID=1000
ARG BUILD_OUTPUT=dist

RUN addgroup -S appgroup -g ${APP_GID} && \
    adduser -S -u ${APP_UID} -G appgroup appuser

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /workspace/${BUILD_OUTPUT}/ /usr/share/nginx/html/

RUN chown -R appuser:appgroup /usr/share/nginx/html /var/cache/nginx /var/run /var/log/nginx

USER appuser:appgroup

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD wget -qO- http://127.0.0.1:8080/healthz || exit 1

CMD ["nginx", "-g", "daemon off;"]
