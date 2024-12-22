# Step 1: Set the base image for Node.js
FROM node:16

# Step 2: Set the working directory inside the container
WORKDIR /app

# Step 3: Copy the package.json and package-lock.json (or yarn.lock) files to the container
COPY package*.json ./

# Step 4: Install dependencies inside the container
RUN npm install

# Step 5: Copy the rest of the application files into the container
COPY . .

# Step 6: Expose the port the app runs on (default React port is 3000)
EXPOSE 3000

# Step 7: Start the React app
CMD ["npm", "start"]
