# get the image
FROM node:20-alpine 

# creates a user with permissions to run the app
RUN addgroup app && adduser -S -G app app

# set the user to run the app
USER app

# set the working directory
WORKDIR /app

# copy package.json and package-lock.json to the working directory
COPY package*.json ./

# to void error EACCES: permission denied, we change the ownership of the files to the root user
USER root

# change the ownership of the /app directory to the app user
RUN chown -R app:app .

# change the user back to the app user
USER app

# install dependencies
RUN npm install

# copy the rest of the files to the working directory
COPY . . 

# expose port 5173 to tell Docker that the container listens on the specified network ports at run time
EXPOSE 5173

# run the app
CMD npm run dev