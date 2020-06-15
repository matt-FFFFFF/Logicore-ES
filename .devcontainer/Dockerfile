FROM ubuntu:18.04

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # Add sudo support
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # Install basic software and Jupyter notebook
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y git python3-pip wget bash zsh software-properties-common \
    && pip3 install notebook \
    #
    # Install dotnet SDK & Powershell
    && wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb \
    && dpkg -i packages-microsoft-prod.deb \
    && rm -f packages-microsoft-prod.deb \
    && apt-get install -y apt-transport-https \
    && add-apt-repository universe \
    && apt-get update \
    && apt-get install -y dotnet-sdk-3.1 dotnet-runtime-3.1 powershell

USER $USERNAME

# Add .net tools to path
ENV PATH="${PATH}:/home/$USERNAME/.dotnet/tools"

# Install powershell modules, dotnet interactive and jupyter kernels
RUN pwsh -Command "& {Get-PSRepository -Name PSGallery | Set-PSRepository -InstallationPolicy Trusted; Install-Module -Name Az.Accounts -RequiredVersion 1.8.0; Install-Module -Name Az.Resources -RequiredVersion 2.0.1}" \
    && dotnet tool update -v n -g Microsoft.dotnet-interactive --add-source "https://dotnet.myget.org/F/dotnet-try/api/v3/index.json" \
    && dotnet interactive jupyter install

