FROM node:18

# working directory for the app inside the container
WORKDIR /usr/src/app

# copy package and package-lock files
COPY package*.json ./

RUN npm install 

# copy app source code
COPY . .

EXPOSE 8080
CMD ["node", "./bin/www"]