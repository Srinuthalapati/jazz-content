# Base Image and maintainer info
FROM jenkins/jenkins:2.164.1-jdk11
MAINTAINER JazzOSS Team

# Switching to root to configure the image with system packages
USER root
ARG adminpass
ENV JENKINS_USER admin
ENV JENKINS_PASS $adminpass
ENV JAVA_OPTS -Djenkins.install.runSetupWizard=false -Djava.io.tmpdir=/var/tmp
COPY jenkins/default-user.groovy /usr/share/jenkins/ref/init.groovy.d/

ARG goVersion=1.10.3

RUN echo "deb http://ftp.fr.debian.org/debian testing main" >> /etc/apt/sources.list && apt-get update  && apt-get install -y -o APT::Immediate-Configure=0 --no-install-recommends --no-upgrade vim curl \
sudo libtool autoconf make unzip rsync gcc autogen shtool pkg-config lsb-release groff less libxml-xpath-perl zip python3 \
python3-dev python3-venv build-essential libssl-dev libffi-dev && ln -sf /usr/bin/python3.9 /usr/bin/python3 && \
curl https://bootstrap.pypa.io/get-pip.py | /usr/bin/python3 && pip --no-cache-dir install --upgrade awscli --ignore-installed six &&  apt-get install -y virtualenv && apt-get clean && \
wget -O /opt/apache-maven-3.5.2-bin.tar.gz https://archive.apache.org/dist/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz && \
tar xzvf /opt/apache-maven-3.5.2-bin.tar.gz -C  /opt && export PATH=$PATH:/opt/apache-maven-3.5.2/bin >> /etc/profile.d/maven.sh && ln -sf /opt/apache-maven-3.5.2/bin/mvn /usr/bin/mvn && \
sed -i '/ftp.fr.debian.org/d' /etc/apt/sources.list && apt-get update && curl -sL https://deb.nodesource.com/setup_12.x | bash && apt-get install -y nodejs && npm install  -global serverless@1.79.0 @angular/cli@1.7.3 jshint && \
wget -O /opt/sonar-scanner-cli-3.0.3.778-linux.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-3.0.3.778-linux.zip && \
unzip /opt/sonar-scanner-cli-3.0.3.778-linux.zip -d /opt && chown -R jenkins:jenkins /opt/sonar-scanner-3.0.3.778-linux && \
ln -sf /opt/sonar-scanner-3.0.3.778-linux/bin/sonar-scanner /bin/sonar-scanner && mkdir /var/log/depcheck_jazz/ && \
mkdir /var/log/depcheck_jazz/nistfiles && chown -R jenkins:jenkins /var/log/depcheck_jazz/ && \
wget -O /opt/dependency-check-3.2.1-release.zip https://bintray.com/jeremy-long/owasp/download_file?file_path=dependency-check-3.2.1-release.zip && \
unzip /opt/dependency-check-3.2.1-release.zip -d /opt && chown -R jenkins:jenkins /opt/dependency-check && \
ln -sf /opt/dependency-check/bin/dependency-check.sh /bin/dependency-check.sh && \
wget -O /opt/google-chrome-stable_current_amd64.deb https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb && apt install -y /opt/google-chrome-stable_current_amd64.deb && \
wget -O /opt/checkstyle-7.6-all.jar https://github.com/checkstyle/checkstyle/releases/download/checkstyle-7.6.1/checkstyle-7.6.1-all.jar && \
apt-get autoremove -y &&  rm -rf /var/lib/apt/lists/* && curl -O https://storage.googleapis.com/golang/go${goVersion}.linux-amd64.tar.gz && tar -xvf go${goVersion}.linux-amd64.tar.gz && mv go /usr/local && \
wget https://aka.ms/downloadazcopy-v10-linux -O /opt/azurecopy.tar.gz && tar xzvf /opt/azurecopy.tar.gz -C  /opt && ln -sf /opt/azcopy_linux_amd64_10.2.1/azcopy /usr/bin/azcopy && curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
ENV GOROOT /usr/local/go
ENV GOBIN  /usr/local/go/bin
ENV GOPATH /usr/local/go/src
ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk-amd64/
ENV PATH $GOROOT:$GOBIN:$GOPATH:$JAVA_HOME:$PATH


COPY jenkins/plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh && /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt
# copy files to handle config and scriptApproval etc
ADD jenkins/files/xmls.tar /var/jenkins_home/
COPY  jenkins/files/copy_reference_file.log /usr/share/jenkins/ref/copy_reference_file.log
COPY  jenkins/files/encrypt.groovy /usr/share/jenkins/ref/encrypt.groovy
COPY  jenkins/files/credentials.xml /usr/share/jenkins/ref/credentials.xml
COPY  jenkins/files/nodeMonitors.xml /usr/share/jenkins/ref/nodeMonitors.xml
COPY  jenkins/files/config.xml /usr/share/jenkins/ref/config.xml
COPY  jenkins/files/scriptApproval.xml /usr/share/jenkins/ref/scriptApproval.xml

USER jenkins
# Expose the jenkins port
EXPOSE 8081
