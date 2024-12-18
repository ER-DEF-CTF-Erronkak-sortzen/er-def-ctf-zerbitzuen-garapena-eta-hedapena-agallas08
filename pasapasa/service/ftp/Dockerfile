FROM debian:latest
RUN apt update && apt install -y vsftpd

COPY ./vsftpd.conf /etc/vsftpd.conf
COPY ./chroot_list /etc/vsftpd.chroot_list
COPY ./.ssh_users /tmp/.ssh_users

RUN mkdir /var/run/vsftpd
RUN chmod 755 /var/run/vsftpd
RUN mkdir /var/run/vsftpd/empty
RUN chmod 755 /var/run/vsftpd/empty

RUN useradd -m odin && echo "odin:e5f6g7h8" | chpasswd
RUN chmod 700 /home/odin
RUN useradd -m idunn && echo "idunn:I9j0K1l2" | chpasswd
RUN chmod 700 /home/idunn
RUN useradd -m freyja && echo "freyja:M3n4O5p6" | chpasswd
RUN chmod 700 /home/freyja
RUN useradd -m thor && echo "thor:Q7r8S9t0" | chpasswd
RUN chmod 700 /home/thor
RUN useradd -m test && echo "test:U1v2W3x4" | chpasswd
RUN chmod 700 /home/test
COPY ./.userlist /home/test/.userlist


# Restart FTP server
CMD ["vsftpd"]