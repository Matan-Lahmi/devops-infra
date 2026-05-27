FROM jenkins/agent:latest
USER root

RUN apt-get update && apt-get install -y \
    docker.io \
    python3 \
    python3-pip \
    python3-venv \
    nodejs \
    npm \
    docker-compose \
    && apt-get clean

RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

RUN pip3 install \
    flake8==7.2.0 \
    pip-audit==2.7.3 \
    pytest==8.3.5 \
    --break-system-packages

ARG DOCKER_GID=999
RUN groupmod -g ${DOCKER_GID} docker && usermod -aG docker jenkins

USER jenkins

