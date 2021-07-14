FROM debian

LABEL app="Teste1"
ENV ANDERSON="testando"
RUN apt-get update && apt-get install -y stress && apt-get clean

CMD stress --cpu 1 --vm-bytes 64M --vm1
