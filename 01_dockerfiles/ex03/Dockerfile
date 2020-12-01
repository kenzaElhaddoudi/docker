FROM ubuntu

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update -y && apt-get install ca-certificates curl openssh-server vim git tzdata -y

WORKDIR /tmp

RUN curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
RUN bash script.deb.sh
RUN apt-get install gitlab-ce -y
RUN echo "gitlab_rails['gitlab_shell_ssh_port'] = 2222" >> /etc/gitlab/gitlab.rb
RUN sed -i "s|external_url 'http://gitlab.example.com'|external_url 'https://10.11.5.9'|g" /etc/gitlab/gitlab.rb
RUN echo "Port 2222" >> /etc/ssh/sshd_config
RUN echo "PubkeyAuthentication yes" >> /etc/ssh/sshd_config
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout gitlab.key -out gitlab.crt -subj "/C=13/ST=13/L=13/O=13/OU=13 Department/CN=10.12.10.14"
RUN echo "nginx['enable'] = true" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['redirect_http_to_https'] = true" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate'] = \"/tmp/gitlab.crt\"" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate_key'] = \"/tmp/gitlab.key\"" >> /etc/gitlab/gitlab.rb

ENTRYPOINT /opt/gitlab/embedded/bin/runsvdir-start & \
            gitlab-ctl reconfigure && service ssh restart && tail -f /dev/null

EXPOSE 80 443 22
