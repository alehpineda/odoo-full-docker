FROM ubuntu:14.04
MAINTAINER Luis Triana <luis.triana@jarsa.com.mx>

RUN echo 'APT::Get::Assume-Yes "true";' >> /etc/apt/apt.conf \
    && echo 'APT::Get::force-yes "true";' >> /etc/apt/apt.conf
RUN locale-gen fr_FR \
    && locale-gen en_US.UTF-8 \
    && dpkg-reconfigure locales \
    && update-locale LANG=en_US.UTF-8 \
    && update-locale LC_ALL=en_US.UTF-8
RUN ln -s /usr/share/i18n/SUPPORTED /var/lib/locales/supported.d/all \
    && locale-gen
ENV PYTHONIOENCODING utf-8
ENV TERM xterm
RUN apt-get update -q && apt-get upgrade -q && \
    apt-get install --allow-unauthenticated -q \
    wget
RUN wget -O - http://apt.postgresql.org/pub/repos/apt/ACCC4CF8.asc | apt-key add -
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list
RUN apt-get update -q && apt-get upgrade -q && \
    apt-get install --allow-unauthenticated -q \
    aptitude \
    antiword \
    build-essential \
    sudo \
    gcc \
    ghostscript \
    git \
    graphviz \
    lptools \
    make \
    mc \
    nano \
    openssh-server \
    openssl \
    poppler-utils \
    postgresql-9.5 \
    postgresql-client-9.5 \
    postgresql-contrib-9.5 \
    postgresql-server-dev-9.5 \
    python-babel \
    python-dateutil \
    python-dev \
    python-docutils \
    python-egenix-mxdatetime \
    python-feedparser \
    python-gdata \
    python-imaging \
    python-jinja2 \
    python-ldap \
    python-libxslt1 \
    python-lxml \
    python-mako \
    python-matplotlib \
    python-mock \
    python-openid \
    python-openssl \
    python-paramiko \
    python-pdftools \
    python-pip \
    python-psutil \
    python-psycopg2 \
    python-pybabel \
    python-pychart \
    python-pydot \
    python-pyparsing \
    python-reportlab \
    python-reportlab-accel \
    python-setuptools \
    python-simplejson \
    python-soappy \
    python-tz \
    python-unittest2 \
    python-vatnumber \
    python-vobject \
    python-webdav \
    python-werkzeug \
    python-xlwt \
    python-yaml \
    python-zbar \
    python-zsi \
    qrencode \
    wget \
    xmlstarlet \
    sudo \ 
    xsltproc \
    zbar-tools

# Install wkhtmltopdf
RUN cd /tmp && \
    wget https://downloads.wkhtmltopdf.org/obsolete/linux/wkhtmltopdf-0.9.9-static-amd64.tar.bz2 && \
    tar xvjf wkhtmltopdf-0.9.9-static-amd64.tar.bz2 && \ 
    mv wkhtmltopdf-amd64 /usr/bin/wkhtmltopdf && \
    chmod +x /usr/bin/wkhtmltopdf

#Install QR tools
RUN cd /tmp && \
    wget https://launchpad.net/qr-tools/trunk/1.2/+download/python-qrtools_1.2_all.deb && \
    dpkg -i python-qrtools_1.2_all.deb

# Cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* && rm -rf /tmp/*

# Add ODOO user
RUN adduser --home=/home/odoo-7.0/ --disabled-password --gecos "" --shell=/bin/bash odoo
RUN echo 'root:odoo**' | chpasswd 
RUN echo "odoo ALL=(root) NOPASSWD:ALL" | tee -a /etc/sudoers.d/user && chmod 0440 /etc/sudoers.d/user

# Create odoo-server.conf
ADD files/odoo-server.conf /home/odoo-7.0/odoo-server.conf
RUN chown odoo /home/odoo-7.0/odoo-server.conf && \
    chmod +x /home/odoo-7.0/odoo-server.conf

# Create odoo-server service
ADD files/odoo-server /etc/init.d/odoo-server
RUN chown odoo /etc/init.d/odoo-server && \
    chmod +x /etc/init.d/odoo-server

#Install Odoo
RUN cd /home/odoo-7.0/ && git clone https://github.com/odoo/odoo.git -b 7.0 odoo
RUN chown -R odoo:odoo /home/ && chmod +x /home/odoo-7.0/odoo

USER postgres

# Run Postgres Server
RUN /etc/init.d/postgresql start && \
    psql --command "CREATE USER odoo WITH SUPERUSER PASSWORD 'odoo';"

USER odoo

CMD ["usr/bin/service","postgresql", "start"]
CMD ["usr/bin/service", "odoo-server", "start"]
CMD /bin/bash

EXPOSE 8069
EXPOSE 8072
EXPOSE 22
EXPOSE 5432
