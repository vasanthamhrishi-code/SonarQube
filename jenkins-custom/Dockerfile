FROM jenkins/jenkins:lts

USER root

# ============================================
# Install System Dependencies & Utilities
# ============================================
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    python3-venv \
    curl \
    wget \
    unzip \
    git \
    && rm -rf /var/lib/apt/lists/*

# ============================================
# Install Docker CLI
# ============================================
RUN curl -fsSL https://get.docker.com -o get-docker.sh && \
    sh get-docker.sh && \
    rm get-docker.sh

# ============================================
# Install Sonar Scanner
# ============================================
RUN wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-6.0.0.4432-linux.zip && \
    unzip sonar-scanner-cli-6.0.0.4432-linux.zip && \
    mv sonar-scanner-6.0.0.4432-linux /opt/sonar-scanner && \
    rm sonar-scanner-cli-6.0.0.4432-linux.zip

# ============================================
# Add Sonar Scanner To PATH
# ============================================
ENV PATH="${PATH}:/opt/sonar-scanner/bin"

# ============================================
# Verify Installations
# ============================================
RUN python3 --version && \
    pip3 --version && \
    docker --version && \
    sonar-scanner --version

# Switch back to the standard Jenkins user for running the controller
USER jenkins


# ============================================
# Install Trivy
# ============================================
USER root

RUN apt-get update && apt-get install -y wget apt-transport-https gnupg lsb-release

# Download and save the official Aqua Security GPG key securely
RUN wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | tee /usr/share/keyrings/trivy.gpg > /dev/null

# Fix: We use 'generic' instead of '$(lsb_release -sc)' to guarantee package compatibility
RUN echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | tee /etc/apt/sources.list.d/trivy.list

# Update the package lists with the new repo and install Trivy, cleaning up files afterward
RUN apt-get update && apt-get install -y trivy && rm -rf /var/lib/apt/lists/*

# Switch back to the standard secure user at the very end of your file
USER jenkins