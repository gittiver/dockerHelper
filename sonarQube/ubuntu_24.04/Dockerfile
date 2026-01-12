# Base our image from builder
FROM builder:ubuntu_24.04

# Install SonarQube 'Scanner'
ARG ScanerVersion=4.7.0.2747
ARG ScannerZipName=sonar-scanner-cli-${ScanerVersion}-linux.zip
RUN wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${ScannerZipName} -O ~/${ScannerZipName} \
	&& unzip ~/${ScannerZipName} -d ~ \
	&& rm -f ~/${ScannerZipName}

# Add it to the PATH
ENV PATH=$PATH:~/sonar-scanner-${ScanerVersion}-linux/bin
