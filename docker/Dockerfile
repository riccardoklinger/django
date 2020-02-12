# use of an ubuntu base for simplicity and transparency

FROM ubuntu:18.04
MAINTAINER Riccardo Klinger <riccardo.klinger@gmail.com>

# getting postgres
RUN apt-get update && apt-get -y install wget gnupg2
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

# Add PostgreSQL's repository. It contains the most recent stable release
#     of PostgreSQL, ``12``.
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main" > /etc/apt/sources.list.d/pgdg.list

# Install software-properties-common and PostgreSQL 12
#  and some other packages for ftp
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y \
  software-properties-common \
  postgresql-12 \
  postgresql-client-12 \
  postgresql-contrib-12 \
  postgresql-12-postgis-3 \
  postgresql-12-postgis-3-scripts \

# switch USER
USER postgres

# Adjust PostgreSQL configuration so that remote connections to the
# database are possible.
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/12/main/pg_hba.conf

# And add ``listen_addresses`` to ``/etc/postgresql/12/main/postgresql.conf``
RUN echo "listen_addresses='*'" >> /etc/postgresql/12/main/postgresql.conf

# Expose the PostgreSQL port
EXPOSE 5432

# Create a PostgreSQL role named ``osmdata`` with ``osmdata`` as the password and
# then create a database `osmdata` owned by the ``osmdata`` role and add
# the postgis extension

RUN    /etc/init.d/postgresql start &&\
    psql --command "CREATE USER admin WITH SUPERUSER PASSWORD 'admin';" &&\
    createdb -O data data &&\
    psql -d osmdata --command "CREATE EXTENSION IF NOT EXISTS postgis;" &&\
    psql -d osmdata --command "CREATE EXTENSION IF NOT EXISTS postgis_topology;" &&\
    psql -d osmdata --command "CREATE EXTENSION hstore;" &&\
    psql -d osmdata --command "CREATE SCHEMA import;"


# Add VOLUMEs to allow backup of config, logs and databases
VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]

# Set the default command to run when starting the container
CMD ["/usr/lib/postgresql/12/bin/postgres", "-D", "/var/lib/postgresql/12/main", "-c", "config_file=/etc/postgresql/12/main/postgresql.conf"]
