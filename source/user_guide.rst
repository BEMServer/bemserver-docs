.. _user_guide:

==========
User guide
==========

Basic concepts
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

BEMServer data model stores properties about building and timeseries. By
default, only a few properties are defined. Users may define custom properties.
Custom building or timeseries properties are defined at application level and
therefore may be used to add metadata to builidings and timeseries in all
campaigns.

First steps
===========

Users with admin permissions manage users and campaigns.

The installation guide describes how to create an admin user from the command
line. Other users may be later created and managed from the web interface.

First, create users and user groups. Keep in mind that user groups will be used
to manage permissions.

Then create a campaign. Define campaign scopes to group campaign data. Those
are used to define permissions but also logical groups to filter data.

.. note::

   Users may prefer not to use campaign scopes if there is no need for
   fine-grained permissions (everyone can see everything). A default campaign
   scope is still required.

If needed, define building and timeseries properties.

Define building information. Doing this manually in the web interface being
tedious, the information may be imported from CSV files.

Define timeseries information. Doing this manually in the web interface being
tedious, the information may be imported from a CSV file.

Properties may be added to CSV files assuming they have been defined at
application level first.

At this point, it is possible to send timeserie data to BEMServer trough the
API. Timeseries data may also be imported manually as CSV.
