FROM debian:jessie
MAINTAINER Odoo S.A. <info@odoo.com>

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get upgrade -y \
        && apt-get install -y -f --no-install-recommends \
            ca-certificates \
            curl \
            node-less \
            python-gevent \
            python-pip \
            python-pyinotify \
            python-passlib \
            python-renderpm \
            python-support \
            git-core \
            python-cups python-dateutil python-decorator python-docutils python-feedparser \
            python-gdata python-geoip python-imaging python-jinja2 python-ldap python-libxslt1 \
            python-lxml python-mako python-mock python-openid python-psutil python-psycopg2 \
            python-pybabel python-pychart python-pydot python-pyparsing python-pypdf python-reportlab python-requests \
            python-simplejson python-tz python-unicodecsv python-unittest2 python-vatnumber python-vobject \
            python-werkzeug python-xlwt python-yaml node-clean-css node-less xfonts-75dpi xfonts-base \
        && curl -o wkhtmltox.deb -SL http://nightly.odoo.com/extra/wkhtmltox-0.12.1.2_linux-jessie-amd64.deb \
        && echo '40e8b906de658a2221b15e4e8cd82565a47d7ee8 wkhtmltox.deb' | sha1sum -c - \
        && dpkg --force-depends -i wkhtmltox.deb \
        && apt-get -y install -f --no-install-recommends \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb \
        && pip install psycogreen==1.0

# Install Odoo
ENV ODOO_VERSION 10.0
ENV ODOO_RELEASE 20161201
RUN set -x; \
        adduser --system --quiet --home=/opt/odoo --gecos 'ODOO' --group odoo \
        && git clone --single-branch --branch ${ODOO_VERSION} https://github.com/odoo/odoo.git /opt/odoo/v${ODOO_VERSION} \
        && chown -R odoo /opt/odoo \
        && ln -s /opt/odoo/v${ODOO_VERSION}/odoo-bin /usr/local/bin/odoo

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /usr/local/bin/
RUN mkdir -p /etc/odoo
RUN mkdir -p /var/lib/odoo/log/
COPY ./odoo.conf /etc/odoo/odoo-server.conf
RUN chown odoo /etc/odoo/odoo-server.conf
RUN chown odoo:root /var/lib/odoo/log/

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R odoo /mnt/extra-addons
RUN mkdir -p /var/lib/odoo \
        && chown -R odoo /var/lib/odoo
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8072

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
CMD ["odoo"]
