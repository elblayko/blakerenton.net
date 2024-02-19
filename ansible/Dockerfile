FROM python:latest
ENV PATH=$PATH:/home/ansible/.local/bin

RUN \
  useradd -m ansible && \
  mkdir -p /etc/ansible && \
  mkdir -p /ansible/playbooks && \
  chown -R ansible:ansible /ansible

USER ansible
RUN pip install --user ansible passlib

WORKDIR /ansible/playbooks
