FROM node:18-slim AS app

# Puppeteer uses system Chrome – prevent Chromium download
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true

# Add all build-time args
ARG PUBLIC_SUPABASE_ANON_KEY
ARG PUBLIC_SUPABASE_URL
ARG CLOUDFLARE_BUCKET_DOMAIN
ARG CLOUDFLARE_ACCESS_KEY
ARG CLOUDFLARE_SECRET_ACCESS_KEY
ARG CLOUDFLARE_ACCOUNT_ID
ARG CLOUDFLARE_BUCKET_ID
ARG SMTP_HOST
ARG SMTP_USER
ARG SMTP_PASSWORD
ARG SMTP_PORT
ARG SMTP_SENDER

# Install system dependencies & Chrome
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl gnupg ca-certificates wget fonts-liberation libatk-bridge2.0-0 \
    libnspr4 libnss3 libxss1 libasound2 libxshmfence1 libgbm1 \
    libgtk-3-0 libu2f-udev \
 && curl -fsSL https://dl-ssl.google.com/linux/linux_signing_key.pub | gpg --dearmor -o /usr/share/keyrings/google.gpg \
 && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/google.gpg] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google.list \
 && apt-get update && apt-get install -y google-chrome-stable \
 && rm -rf /var/lib/apt/lists/*

# Create app folder
WORKDIR /app

# Install pnpm
RUN npm install -g pnpm

# Install dependencies
COPY ./package.json ./
RUN pnpm install --frozen-lockfile

# Copy full source
COPY . .

# Build backend
RUN pnpm build

# Set runtime env vars
ENV NODE_ENV=production \
    PUBLIC_SUPABASE_ANON_KEY=${PUBLIC_SUPABASE_ANON_KEY} \
    PUBLIC_SUPABASE_URL=${PUBLIC_SUPABASE_URL} \
    CLOUDFLARE_BUCKET_DOMAIN=${CLOUDFLARE_BUCKET_DOMAIN} \
    CLOUDFLARE_ACCESS_KEY=${CLOUDFLARE_ACCESS_KEY} \
    CLOUDFLARE_SECRET_ACCESS_KEY=${CLOUDFLARE_SECRET_ACCESS_KEY} \
    CLOUDFLARE_ACCOUNT_ID=${CLOUDFLARE_ACCOUNT_ID} \
    CLOUDFLARE_BUCKET_ID=${CLOUDFLARE_BUCKET_ID} \
    SMTP_HOST=${SMTP_HOST} \
    SMTP_USER=${SMTP_USER} \
    SMTP_PASSWORD=${SMTP_PASSWORD} \
    SMTP_PORT=${SMTP_PORT} \
    SMTP_SENDER=${SMTP_SENDER} \
    PORT=3002

EXPOSE 3002

CMD ["pnpm", "start"]
