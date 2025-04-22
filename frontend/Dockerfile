# Base image with Node.js
FROM node:16-alpine

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application
COPY . .

# Expose Angular dev server port
EXPOSE 4200

# Start the app
CMD ["npm", "start"]