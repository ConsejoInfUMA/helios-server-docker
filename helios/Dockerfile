FROM debian:bookworm

# Dependencies
RUN apt-get update && apt-get install -y python3 python3-pip postgresql-client-15 git libpq-dev libldap-dev libsasl2-dev && rm -rf /var/lib/apt/lists/*

RUN git clone https://github.com/benadida/helios-server /opt/helios

# Disable SSL for DB connection
RUN sed -i "s|DATABASES\['default'\] = dj_database_url\.config(conn_max_age=600, ssl_require=True)|DATABASES['default'] = dj_database_url.config(conn_max_age=600, ssl_require=False)|" "/opt/helios/settings.py"

RUN chmod +x /opt/helios/reset.sh

WORKDIR /opt/helios

# Build
RUN pip3 install --break-system-packages -r requirements.txt

# Cleanup
RUN apt-get remove -y git libpq-dev libldap-dev libsasl2-dev

# Config
COPY ./config.py /tmp

RUN sed -i -e '$a\' /opt/helios/settings.py
RUN cat /tmp/config.py >> /opt/helios/settings.py

# Entrypoint
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod 777 /usr/local/bin/entrypoint.sh

EXPOSE 8000

ENTRYPOINT ["/bin/bash", "/usr/local/bin/entrypoint.sh"]
