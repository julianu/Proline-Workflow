FROM nfcore/base

LABEL authors="Veit Schwaemmle, David Bouyssie" \
      description="Docker image containing all requirements for nf-core-proline-labelfree"

### Setup conda
COPY environment.yml /
RUN conda env create -f /environment.yml && conda clean -a
ENV PATH /opt/conda/envs/nf-core-proline-labelfree/bin:$PATH

### Install some required unix tools
RUN apt-get update; apt-get install -y curl zip unzip ; apt-get clean

### Files will be changed by searchgui and thus need to be readable by any user
RUN chmod -R a+rwx /opt/conda/envs/nf-core-proline-labelfree/share/searchgui-*/resources
RUN mv /opt/conda/envs/nf-core-proline-labelfree/share/searchgui-*/resources /home/; \
    ln -s /home/resources /opt/conda/envs/nf-core-proline-labelfree/share/searchgui-*/

### TODO: explain why this mkdir is required
RUN update-ca-certificates && mkdir /usr/share/man/man1

### Install JDK11 (already done via conda)
#RUN apt-get update; apt-get install -y openjdk-11-jdk; apt-get clean 

### Very dirty solution to get java 8 in as it is needed for Proline
RUN apt-get update; apt-get install -y apt-transport-https ca-certificates dirmngr gnupg software-properties-common wget; \
    wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | apt-key add -; \
    add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/; \
    apt update; apt install -y adoptopenjdk-8-hotspot; \
    ln -s /usr/lib/jvm/adoptopenjdk-8-hotspot-amd64/bin/java /usr/bin/java8

### Install Java 8 (needed for SearchGUI and Proline) with SDK MAN
#SHELL ["/bin/bash", "-c"]
#RUN curl -s "https://get.sdkman.io" | bash
#RUN source "/root/.sdkman/bin/sdkman-init.sh"; sdk version; sdk install java 8.0.265.hs-adpt
#ENV SDKMAN_DIR="/root/.sdkman/"
#ENV JAVA_HOME="$SDKMAN_DIR/candidates/java/current"
#ENV PATH="$JAVA_HOME/bin:$PATH"
#RUN export JAVA_HOME; export PATH
#SHELL ["/bin/sh", "-c"]
#RUN java -version

### Install Mono (required for thermo2mzdb execution)
RUN apt install -y mono-complete

### Install mzDB tools
RUN mkdir mzdb-tools ; cd mzdb-tools; \
    wget https://github.com/mzdb/mzdb4s/releases/download/0.2/mzdb-conversion-tools_0.2.zip; \
    unzip mzdb-conversion-tools_0.2.zip; cd ..; \
    chmod a+x /mzdb-tools/run_thermo2mzdb.sh; chmod a+x /mzdb-tools/thermo2mzdb; ln -s /mzdb-tools/run_thermo2mzdb.sh /usr/bin/thermo2mzdb; \
    chmod a+x /mzdb-tools/run_mzdb2mgf.sh; chmod a+x /mzdb-tools/mzdb2mgf; ln -s /mzdb-tools/run_mzdb2mgf.sh /usr/bin/mzdb2mgf

### Install Proline CLI
RUN wget https://github.com/profiproteomics/proline-cli/releases/download/0.2.0-SNAPSHOT-2019-10-04/proline-cli-0.2.0-SNAPSHOT-bin.zip; \
    unzip proline-cli-0.2.0-SNAPSHOT-bin.zip; mv proline-cli-0.2.0-SNAPSHOT proline; \
    chmod -R a+rwx ./proline/ ; \
    mkdir proline_results ; \
    chmod -R a+rwx ./proline_results/
