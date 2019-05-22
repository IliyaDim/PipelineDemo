FROM ubuntu:18.04

# Prevent errors about having no terminal when using apt-get
ENV DEBIAN_FRONTEND noninteractive


RUN apt-get update && apt-get install -y --no-install-recommends curl

# Set up exclusions
# COPY excludes*txt /tmp/

# Install ACE v11.0.0.4 and accept the license
RUN mkdir /opt/ibm && \
    curl http://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/integration/11.0.0.4-ACE-LINUX64-DEVELOP.tar.gz \
    | tar zx --exclude ace-11.0.0.4/tools --directory /opt/ibm && \
    mv /opt/ibm/ace-11.0.0.4 /opt/ibm/ace-11 && \
    /opt/ibm/ace-11/ace make registry global accept license silently

# The product is now installed, and everything below is ease-of-use enhancements.


# Copy in script files - this is used to prevent the "mqsiprofile
# repetition disallowed" message from being printed on shell start.
COPY ace_env.sh /usr/local/bin/

# Configure the system and increase security
RUN echo "ACE_11:" > /etc/debian_chroot \
  && sed -i 's/sha512/sha512 minlen=8/' /etc/pam.d/common-password \
  && sed -i 's/PASS_MIN_DAYS\t0/PASS_MIN_DAYS\t1/' /etc/login.defs \
  && sed -i 's/PASS_MAX_DAYS\t99999/PASS_MAX_DAYS\t90/' /etc/login.defs \
  && echo ". /usr/local/bin/ace_env.sh" >> /root/.bashrc

# Set BASH_ENV to source mqsiprofile when using docker exec bash -c
ENV BASH_ENV=/usr/local/bin/ace_env.sh

# Create a user to run as, create the ace workdir, and chmod script files
RUN useradd --create-home --home-dir /home/aceuser --shell /bin/bash -G mqbrkrs,sudo aceuser \
  && su - aceuser -c '. /opt/ibm/ace-11/server/bin/mqsiprofile && mqsicreateworkdir /home/aceuser/ace-server' \
  && echo ". /usr/local/bin/ace_env.sh" >> /home/aceuser/.bashrc \
  && chmod 755 /usr/local/bin/*


USER aceuser
ENTRYPOINT ["bash"]
