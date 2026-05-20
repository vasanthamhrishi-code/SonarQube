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


