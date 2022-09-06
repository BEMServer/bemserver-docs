.. _installation:

============
Installation
============

Architecture
============

BEMServer is made of several software bricks.

BEMServer Core
--------------

BEMServer Core is the central part. It is responsible for the connection with
the database and the authorization and authorization layer.

It exposes objects that can be used by other blocks.

It is meant to be imported and used by trusted code as it gives direct access
to the database.

It is used internally by BEMServer API.

It also contains the scheduler responsible for periodic tasks.

BEMServer API
-------------

BEMServer API is a web REST API that exposes the core data model and features.
It can be exposed over the Internet to be accessed by external modules.

It exposes its own documentation in the OpenAPI format for module developers.

BEMServer UI
------------

BEMServer UI is the web interface to BEMServer. It communicates with the core
via BEMServer API.

This is the interface that is meant to be exposed to final users.

It provides an interface for both administrative tasks (user management,...)
and data analysis (data visualization).


Install BEMServer
=================

To get BEMServer up and running, one needs to run both BEMServer API and
BEMServer UI.

All BEMServer blocks are written in Python and require Python 3.9+.

The instructions that follow are tested on a Debian 11 (Bullseye) system.

Prerequisites
-------------

BEMServer relies on PostgreSQL and the TimescaleDB extension.

First, install PostgreSQL 13::

    $ aptitude install postgresql

TimescaleDB is not in the Debian repositories. Follow instructions in
https://docs.timescale.com/latest/getting-started/setup

.. note::
    There is no need to install PostgreSQL from external repositories as
    suggested in TimescaleDB docs.

Create a database and install TimescaleDB extension (should be done after each
TimescaleDB update).

Assuming the user has database creation permission:::

    $ createdb bemserver
    $ psql -U $USER -d bemserver -c "CREATE EXTENSION IF NOT EXISTS timescaledb"

.. note::
    Later, when updating timescaledb, the extension in the database should be updated too::

    $ psql -U $USER -d bemserver -c "ALTER EXTENSION timescaledb UPDATE;"

Install prerequisites for psycopg2 compilation (used by BEMServer Core)::

    $ aptitude install python3-dev libpq-dev

The task scheduler uses Redis database as broker::

    $ aptitude install redis

BEMServer API
-------------

.. note::
    It is advised to work in a dedicated virtual environment.

Install bemserver_api::

    $ pip install bemserver_core

Database setup
^^^^^^^^^^^^^^

Set DB URI in an environment variable (adapt line with actual values)::

    $ export SQLALCHEMY_DATABASE_URI="postgresql+psycopg2://user:password@localhost:5432/bemserver"

Use `setup_db` command to initialize a clean database::

    $ bemserver_setup_db

Create an admin user::

    $ bemserver_create_user --name chuck --email chuck@norris.com --admin

API setup
^^^^^^^^^

Create a configuration file (e.g. bemserver-api.cfg). Add API parameters:

.. code-block::

       SQLALCHEMY_DATABASE_URI="postgresql+psycopg2://user:password@localhost:5432/bemserver"

Set an environment variable to point to that file::

    $ export FLASK_SETTINGS_FILE="/path/to/bemserver-api.cfg"

At this point, the web API can be launched from the command line::

    $ flask run

It can be accessed in a local browser at http://localhost:5000.

.. warning::
    While this is fine in development mode, production setups should use a real
    webserver such as Apache or Nginx.

BEMServer UI
------------

.. note::
    It is advised to work in a dedicated virtual environment.

Install bemserver_ui::

    $ pip install bemserver_ui

UI setup
^^^^^^^^

Create a configuration file (e.g. bemserver-ui.cfg). Add UI parameters:

.. code-block::

    BEMSERVER_API_HOST = "localhost:5000"
    BEMSERVER_API_USE_SSL = False

Set an environment variable to point to that file::

    $ export FLASK_SETTINGS_FILE="/path/to/bemserver-ui.cfg"

At this point, the web UI can be launched from the command line::

    $ flask run -p 5001

.. note::
    Flask uses port 5000 by default. Be sure to specify another port for either
    bemserver_api or bemserver_ui to avoid a conflict.

.. warning::
    While this is fine in development mode, production setups should use a real
    webserver such as Apache or Nginx.

Scheduled Tasks
---------------

BEMServer uses `Celery`_ to manage asynchronous tasks.

Open two shells in an environment where bemerver-core is installed, and in each
shell, define an environment variable with the DB URI::

    $ export SQLALCHEMY_DATABASE_URI="postgresql+psycopg2://user:password@localhost:5432/bemserver"

In a shell, start Celery workers to execute the tasks::

    $ celery -A bemserver_core worker

In the other, start Celery beat to trigger tasks at regular intervals::

    $ celery -A bemserver_core beat

.. warning::
    While this is fine in development mode, production setups should use a
    daemon, e.g. by defining a systemd service.


.. _Celery: https://docs.celeryq.dev/
