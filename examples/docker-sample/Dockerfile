# Use an existing docker image as a base
FROM alpine

# Download and install its dependency
RUN apk add --update redis

# Tell the image what to do when it start as a container
CMD ["redis-server"]
