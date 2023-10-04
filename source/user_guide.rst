.. _user_guide:

==========
User guide
==========

Basic Concepts
==============

The high-level entity for a project is the campaign. A campaign may span on
several sites and buildings. It has beginning and end dates, although the end
date may be left open to let the campaign last indefinitely.

Users are associated to user groups. User groups provide access to campaigns.

Buildings are described in a tree structure inspired from the IFC model: a site
is a geographical unit containing buildings, which contain stores, which
contain spaces. Zone is an orthogonal concept: zones may span over multiple
space parts.

Timeseries and events data in a campaign may be split into separate scopes.
Campaign scopes are used to organize data and to provide more fine-grained
permissions.

Timeseries data may have several states depending on their origin and the
processes they have been through. For a given timeseries name and ID, the
database stores data for each state. By default, BEMServer defines the "Raw"
and the "Clean" states. The "Raw" state is intended for raw data from sensors,
while the "Clean" state is meant for data pre-processed by a cleanup process or
data from a reliable source. Custom data states may be added.

BEMServer data model stores properties about building and timeseries. By
default, only a few properties are defined. Users may define custom properties.
Custom building or timeseries properties are defined at application level and
therefore may be used to add metadata to builidings and timeseries in all
campaigns.

First Steps
===========

Users with admin permissions manage users and campaigns.

The installation guide describes how to create an admin user from the command
line. Other users may be later created and managed from the web interface.

First, create users and user groups. Keep in mind that user groups will be used
to manage permissions.

Then create a campaign. Define campaign scopes to group campaign data. Those
are used to define permissions and are also logical groups used to filter data.

.. note::

   Users may prefer not to use campaign scopes if there is no need for
   fine-grained permissions (everyone can see everything). A default campaign
   scope is still required.

If needed, define building and timeseries properties.

Define building information. Doing this manually in the web interface being
tedious, the information may be imported from CSV files.

Define timeseries information. Doing this manually in the web interface being
tedious, the information may be imported from a CSV file.

Properties may be added to buildings and timeseries through CSV files assuming
they have been defined at application level first.

At this point, it is possible to send timeseries data to BEMServer trough the
API. Timeseries data may also be imported manually as CSV in the web interface.

Events and Notifications
=========================

The concept of events allows to store information that is not timeseries. An
event consists of a timestamp, a level, a category and a description.

Events may be created manually, for instance to record an important event in a
campaign, such as a configuration change, an issue with the sensors, etc. but
generally, events are created by :ref:`services <services>`.

Event levels are ``DEBUG``, ``INFO``, ``WARNING``, ``ERROR`` and ``CRITICAL``,
in ascending order of importance.

BEMServer defines default categories for default services:

- Data missing: no data in a given time interval
- Data present: data present while missing in earlier check
- Outliers: data outside of user-defined bounds
- No outliers: no outlier while there were outliers in earlier check

Events may be associated with timeseries and sites/building/...

Like timeseries, events are associated to a campaign scope.

Events trigger user notifications. Those are sent by email and displayed in the
top-right corner of the web interface.

By default, users receive notifications for an event if they have access to the
campaign scope of the event and the level of the event is at least ``WARNING``.
Users may define custom notification levels for each event category. Thus,
they may opt-in to get ``DEBUG`` or ``INFO`` notifications for missing/present
data, for instance, or opt-out of (almost) all messages by setting ``CRITICAL``
notification level for each event category. This configuration is available in
the user settings menu.
