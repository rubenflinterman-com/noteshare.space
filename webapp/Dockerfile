FROM node:16-alpine AS BUILD_IMAGE

# Install build dependencies including OpenSSL
RUN apk add --no-cache openssl1.1-compat

# Set the working directory
WORKDIR /app

# Install dependencies
COPY package.json package-lock.json ./
RUN npm ci

# Copy all local files into the image
COPY . .

# Generate Prisma client
RUN npx prisma generate

# Build the application
RUN npm run build

# Remove development dependencies
RUN npm prune --production

# Start a new stage for the production image
FROM node:16-alpine

# Install runtime dependencies including OpenSSL
RUN apk add --no-cache openssl1.1-compat

# Set the working directory
WORKDIR /app

# Copy files from the build image
COPY --from=BUILD_IMAGE /app .

# Set environment variables and expose the port
ENV PORT 8080
EXPOSE 8080

# Command to run the application
CMD ["node", "./build/src/server.js"]
