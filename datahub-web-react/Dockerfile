FROM node:18-slim AS build

WORKDIR /app

# Copy GraphQL schemas needed for codegen
COPY datahub-graphql-core/src/main/resources/*.graphql /app/datahub-graphql-core/src/main/resources/

# Copy React app dependency files
COPY datahub-web-react/package.json datahub-web-react/yarn.lock /app/datahub-web-react/

# Install dependencies
WORKDIR /app/datahub-web-react
RUN yarn install --frozen-lockfile

# Copy React app source
COPY datahub-web-react/ /app/datahub-web-react/

# Build (includes graphql-codegen + vite build)
RUN yarn build

# Serve with Caddy
FROM caddy:2-alpine
COPY --from=build /app/datahub-web-react/dist /srv
COPY <<EOF /etc/caddy/Caddyfile
:{$PORT:3000}
root * /srv
file_server
try_files {path} /index.html
EOF
