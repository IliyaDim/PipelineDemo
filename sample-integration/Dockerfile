FROM ace-minimal

# Switch off the admin REST API for the server run, as we won't be deploying anything after start
RUN sed -i 's/#port: 7600/port: -1/g' /home/aceuser/ace-server/server.conf.yaml 

COPY PING_Basic_BAR.bar /tmp/PING_Basic_BAR.bar
RUN bash -c "mqsibar -c -a /tmp/PING_Basic_BAR.bar -w /home/aceuser/ace-server"

# Set entrypoint to run the server
ENTRYPOINT ["bash", "-c", "IntegrationServer -w /home/aceuser/ace-server"]


