# Use node 14 as the base image
FROM node:14

# Set working directory in the container
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json /app/
RUN npm install

# Copy the rest of the application code
COPY . /app

# Expose the port for the frontend (React typically runs on port 3000)
EXPOSE 3000

# Start the application
CMD ["npm", "start"]

