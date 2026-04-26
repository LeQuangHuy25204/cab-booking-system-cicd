# Use Node.js LTS version
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Install system dependencies
RUN apk add --no-cache \
    mongodb-tools \
    redis \
    && rm -rf /var/cache/apk/*

# Copy shared dependency
COPY shared/ /shared/

# Copy package files from root context
COPY services/user-service/package.json services/user-service/package-lock.json ./

# Install dependencies
RUN npm install --omit=dev --force && npm cache clean --force

# Copy source code
COPY services/user-service/src/ ./src/
COPY services/user-service/index.js ./

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nextjs -u 1001

# Change ownership of app directory
RUN chown -R nextjs:nodejs /app
USER nextjs

# Expose port
EXPOSE 3005

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD node -e "require('http').get('http://localhost:3005/api/users/health', (res) => { \
        process.exit(res.statusCode === 200 ? 0 : 1) \
    }).on('error', () => process.exit(1))"

# Start the application
CMD ["node", "index.js"]