# set the base image to create the image
FROM node:20-alpine 

# creates a user with permissions to run the app
# -S: Creates a system user
# -G: Adds the user to the group
# This is done to avoid running the app as root
# If the app is run as root, any vulnerabilities in the app can be exploited to gain access to the host system
# It's a good practice to run the app as a non-root user
RUN addgroup app && adduser -S -G app app

# set the user to run the app
USER app

WORKDIR /app

# copy package.json and package-lock.json to the working directory
# This is done before copying the rest of the files to take advantage of Docker's cache
# If the package.json and package-lock.json files haven't changed, Docker will use cached deps
COPY package*.json ./

# to void error EACCES: permission denied, we change the ownership of the files to the root user
USER root

# change the ownership of the /app directory to the app user
# chown -R <user>:<group> <directory>
# chown command changes the user and/or group ownership of a given file
RUN chown -R app:app .

# change the user back to the app user
USER app

# install dependencies
RUN npm install

# copy the rest of the files to the working directory
COPY . . 

# expose port 5173 to tell Docker that the container listens on the specified network ports at run time
EXPOSE 5173

CMD npm run dev