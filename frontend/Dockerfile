# Use a lightweight Node.js image (Alpine version for reduced size)
FROM node:14-alpine

# Set environment variables
ENV NODE_ENV=production

# Set working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json (if available) to leverage Docker caching
COPY package.json package-lock.json ./

# Install dependencies (production only to reduce size)
RUN npm install --production

# Copy only necessary files to the container
COPY . .

# Build the React application (if it's a React-based frontend)
RUN npm run build

# Expose the port for the frontend
EXPOSE 3000

# Start the application (serve the production build if using React)
CMD ["npm", "start"]

