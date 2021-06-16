FROM ubuntu:16.04

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN apt-get update \
&& apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        jq \
        git \
        iputils-ping \
        libcurl3 \
        libicu55 \
        libunwind8 \
        netcat \
        tree \
        wget \
        apt-transport-https \
        unzip

# Install powershell core
RUN wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb \
        && dpkg -i packages-microsoft-prod.deb \
        && apt-get update \
        && apt-get install -y \
        powershell \
        dotnet-sdk-5.0

RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list | tee /etc/apt/sources.list.d/msprod.list

RUN apt-get update \
&& env ACCEPT_EULA=y apt-get install -y --no-install-recommends \
        mssql-tools \
        unixodbc-dev

# Install AZ CLI
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash
RUN echo "AZURE_EXTENSION_DIR=/usr/local/lib/azureExtensionDir" | tee -a /etc/environment \
    && mkdir -p /usr/local/lib/azureExtensionDir

RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc

WORKDIR /azp

RUN mkdir ./patches
COPY ./patches/AgentService.js ./patches/
COPY ./start.sh .
RUN chmod +x start.sh

CMD ["./start.sh"]