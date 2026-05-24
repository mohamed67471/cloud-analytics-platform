FROM node:20-slim AS builder

WORKDIR /app

RUN corepack enable
RUN corepack prepare pnpm@9.15.0 --activate

ENV DATABASE_URL="postgresql://user:password@localhost:5432/dummy"

COPY package.json pnpm-lock.yaml ./

RUN pnpm install --frozen-lockfile

COPY . .

RUN pnpm run build-docker

# Runtime image 

FROM node:20-slim AS runner

WORKDIR /app

ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1
ENV HOSTNAME=0.0.0.0
ENV PORT=3000

COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public


COPY --from=builder /app/node_modules/@prisma/client ./node_modules/@prisma/client

USER node

EXPOSE 3000

CMD ["node", "server.js"]