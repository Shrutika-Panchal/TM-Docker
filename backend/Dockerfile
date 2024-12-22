# Step 1: Set the base image
FROM node:16

# Step 2: Set the working directory inside the container
WORKDIR /app

# Step 3: Copy the package.json and package-lock.json (or yarn.lock) files to the container
COPY package*.json ./

# Step 4: Install dependencies inside the container
RUN npm install

# Step 5: Copy the rest of the application files into the container
COPY . .

# Step 6: Expose the port the app runs on (default Node.js port or your specific port)
EXPOSE 3001

# Step 7: Command to run your application (run index.js directly)
CMD ["node", "index.js"]
