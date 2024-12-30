# Stage 1: Build the app
FROM node:18 AS builder

# Set the working directory inside the container
WORKDIR /app

# Install global dependencies like turbo
RUN npm install -g turbo

# Copy only the necessary package.json and turbo.json files from the root
COPY package.json package-lock.json turbo.json ./

# Copy the service-specific package.json and its dependencies
COPY apps/service1/package.json apps/service1/package-lock.json ./apps/service-a/

# Copy the packages directory (where the libraries are located)
COPY packages/ packages/

# Copy the app service code (source code)
COPY apps/service1/ apps/service1/

# Install dependencies for the entire monorepo using Turbo
RUN turbo install

# Build the service (including its dependencies from packages/)
RUN turbo run build --filter=apps/service1 --no-cache

# Stage 2: Create the slim runtime image
FROM node:18-slim

# Set the working directory inside the container
WORKDIR /app

# Copy only the built application (dist), node_modules, and service code from the builder stage
COPY --from=builder /app/apps/service1/dist /app/apps/service1/dist
COPY --from=builder /app/node_modules /app/node_modules
COPY --from=builder /app/apps/service1 /app/apps/service1
COPY --from=builder /app/package.json /app/package-lock.json /app/

# Expose the port (adjust to your app's port)
EXPOSE 3000

# Run the app (adjust the start command to fit your app)
#CMD ["npm", "run", "start:prod", "--workspace", "apps/service-a"]

CMD [ "node", "/app/apps/service-a/dist/main.js" ]