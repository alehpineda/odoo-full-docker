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
RUN apt-get update -q && apt-get upgrade -q && \
    apt-get install --allow-unauthenticated -q \
    aptitude \
    build-essential \
    fontconfig \
    git \
    libevent-dev \
    libjpeg-dev \
    libldap2-dev \
    libsasl2-dev \
    libssl-dev \
    libffi-dev \
    libpq-dev \
    libx11-6 \
    libxext6 \
    libxml2-dev \
    libxrender1 \
    libxslt-dev \
    nano \
    npm \
    nodejs \
    node-less \
    openssl \
    python \
    python-dev \
    python-pip \
    python-imaging \
    python-libxml2 \
    python-pandas \
    python-psycopg2 \
    xfonts-75dpi \
    xfonts-base \
    python-qrtools \
    python-openssl \
    openssh-server \
    swig \
    xmlstarlet \
    xsltproc 

    # Install wkhtmltopdf
RUN cd /tmp && \
    wget -O wkhtmltox-0.12.1_linux-trusty-amd64.deb https://downloads.wkhtmltopdf.org/0.12/0.12.1/wkhtmltox-0.12.1_linux-trusty-amd64.deb && \
    dpkg -i wkhtmltox-0.12.1_linux-trusty-amd64.deb

# Install nodejs
RUN ln -s /usr/bin/nodejs /usr/bin/node && npm install -g less less-plugin-clean-css

# Download and install odoo requirements from github.com/odoo/odoo/requirements.txt
RUN cd /tmp && \
    wget -q https://raw.githubusercontent.com/odoo/odoo/10.0/requirements.txt && \
    pip install -r requirements.txt && pip install --upgrade pip

#Python Libraries
RUN pip install suds \
    pyopenssl \
    M2Crypto \
    pillow \
    qrcode \
    xmltodict \
    xlrd \
    cfdilib \
    pytz \
    jinja2 \
    json2html \
    odoorpc \
    ofxparse \
    num2words \
    boto3 \
    cryptography \
    pycrypto

# Cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* && rm -rf /tmp/*

# Add ODOO user
RUN adduser --home=/home/odoo-10.0/ --disabled-password --gecos "" --shell=/bin/bash odoo
RUN echo 'root:odoo**' | chpasswd 
RUN echo "odoo ALL=(root) NOPASSWD:ALL" | tee -a /etc/sudoers.d/user && \
    chmod 0440 /etc/sudoers.d/user

# Create odoo-server.conf
ADD files/odoo-server.conf /home/odoo-10.0/odoo-server.conf
RUN chown odoo /home/odoo-10.0/odoo-server.conf && \
    chmod +x /home/odoo-10.0/odoo-server.conf

# Create odoo-server service
ADD files/odoo-server /etc/init.d/odoo-server
RUN chown odoo /etc/init.d/odoo-server && \
    chmod +x /etc/init.d/odoo-server

#Install Odoo
RUN cd /home/odoo-10.0/ && git clone -b 10.0 --single-branch --depth=1 https://github.com/odoo/odoo.git odoo
RUN chown -R odoo:odoo /home/ && chmod +x /home/odoo-10.0/odoo

USER odoo

CMD /bin/bash

EXPOSE 8069
EXPOSE 8072
EXPOSE 22
