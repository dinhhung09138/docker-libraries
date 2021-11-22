# Specify a base image
# find a base image from docker hub
# uing node base with alpine is a specific tag
FROM node:alpine

# Install some dependencies
WORKDIR /usr/app
COPY ./package.json /usr/app/
RUN npm install
COPY ./ /usr/app/

# Run the defautl comand
CMD ["npm", "start"]