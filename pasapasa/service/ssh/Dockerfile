FROM ubuntu:latest
RUN apt update && apt install -y openssh-server

# Create user
RUN useradd -m loki && echo "loki:K7l8M9n0" | chpasswd
RUN chmod 700 /home/loki

# Configure SSH
RUN mkdir /var/run/sshd

# Start SSH server
CMD ["/usr/sbin/sshd", "-D"]