# ---------- deps: camada cacheada enquanto os manifests nao mudarem ----------
FROM oven/bun:1.3.14-alpine AS deps
WORKDIR /app
COPY package.json bun.lock ./
COPY apps/api/package.json apps/api/
COPY apps/web/package.json apps/web/
COPY packages/typescript-config/package.json packages/typescript-config/
RUN bun install --frozen-lockfile

# ---------- dev: hot reload via `docker compose watch` ----------
FROM deps AS dev
WORKDIR /app
COPY . .
ENV HOSTNAME=0.0.0.0
ENV PORT=3000
WORKDIR /app/apps/web
EXPOSE 3000
CMD ["bun", "run", "dev"]

# ---------- build ----------
FROM deps AS build
WORKDIR /app
COPY . .
RUN cd apps/web && bun run build

# ---------- runtime ----------
FROM oven/bun:1.3.14-alpine AS runtime
WORKDIR /app
ENV NODE_ENV=production
ENV HOSTNAME=0.0.0.0
ENV PORT=3000
COPY --from=build /app/apps/web/.next/standalone ./
COPY --from=build /app/apps/web/.next/static ./apps/web/.next/static
EXPOSE 3000
CMD ["bun", "apps/web/server.js"]
