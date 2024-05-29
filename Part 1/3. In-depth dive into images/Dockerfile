# Start from the alpine image that is smaller but no fancy tools
FROM ubuntu:22.04

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the script.sh file from this directory to /usr/src/app/ creating /usr/src/app/hello.sh
COPY script.sh .

# Alternatively, if we skipped chmod earlier, we can add execution permissions during the build.
RUN chmod +x script.sh

# Install curl
RUN apt update && apt upgrade && apt install -y curl

# When running docker run the command will be ./hello.sh
ENTRYPOINT ["./script.sh"]