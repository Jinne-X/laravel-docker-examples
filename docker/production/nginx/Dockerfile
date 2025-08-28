# Stage 1: Build assets
FROM debian AS builder

# Install Node.js and build tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    nodejs \
    npm \
    && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set working directory
WORKDIR /var/www

# Copy Laravel application code
COPY . /var/www

# Install Node.js dependencies and build assets
RUN npm install && npm run build

# Stage 2: Nginx production image

FROM nginxinc/nginx-unprivileged:stable-alpine


# Copy custom Nginx server configuration to conf.d
COPY ./docker/production/nginx/default.conf /etc/nginx/conf.d/default.conf

# Copy Laravel's public assets from the builder stage
# -----------------------------------------------------------
# We only need the 'public' directory from our Laravel app.
# -----------------------------------------------------------
COPY --from=builder /var/www/public /var/www/public


# Set the working directory to the public folder
WORKDIR /var/www/public

# Ensure log directory exists and is writable by UID 101
RUN mkdir -p /var/log/nginx && chown 101:101 /var/log/nginx


# Expose port 8080 and start Nginx
EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]
