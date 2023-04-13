.. _services:

========
Services
========

The point of BEMServer is not only to store data but also to use it to produce
useful information. This is the role of services.

Services may be internal to BEMServer or remote applications.

Internal services are written in Python and use the bemserver-core object
layer. They are scheduled by the internal task scheduler. They are setup by the
BEMServer instance administrators and run with all permissions.

Extenal services may run on any machine in the network, connecting to BEMServer
via the REST API. This is the best choice for untrusted code. Also, using the
API allows service developers to use their language and framework of choice.

The folling sections describe the internal services shipped with BEMServer.

Cleanup Service
===============

Task name: "Cleanup"

The Cleanup service drops outliers from timeseries. It runs on a 5 minutes
schedule. Once enabled for a campaign, it reads data from all timeseries in the
campaign in "Raw" state, drops outliers and writes to the "Clean" state.

To detect outliers, "Min" and "Max" bounds must be provided as timeseries
attributes when configuring the campaign. Missing bounds are treated as open
interval.

The configuration required from the user is:

- Set "Min" and "Max" properties on timeseries.
- Enable service for each campaign.

.. note::

   For each timeseries, the Cleanup service stores the last processed timestamp
   and starts from there on next iteration. It therefore requires that the data
   is sent in chronlogical order, timeseries-wise. In other words, it doesn't
   matter if e.g. due to a lost connection, some timeseries are sent with a
   delay, but each of them should be sent by ascending timestamps.

Check Missing Data Service
==========================

Task name: "CheckMissing"

Parameters:

- ``period``: one of ``"second"``, ``"minute"``, ``"hour"``, ``"day"``,
  ``"week"``, ``"month"``, ``"year"``
- ``period_multiplier``: integer multiplier, must be 1 for periods of day or more
- ``timezone``: IANA timezone (defaults to UTC)
- ``min_completeness_ratio``: minimum present/expected data to pass check
  (defaults to 0.9)

This service checks the timeseries data for missing data. Missing data is
detected if the expected data interval is known and the amount of data on a
given period is lower than the expected amount multiplied by the
``min_completeness_ratio``. When using wireless sensors in noisy environment
where an important data loss is expected, the minimum ratio should be lowered.

Check Outliers Service
======================

Task name: "CheckOutliers"

Parameters:

- ``period``: one of ``"second"``, ``"minute"``, ``"hour"``, ``"day"``,
  ``"week"``, ``"month"``, ``"year"``
- ``period_multiplier``: integer multiplier, must be 1 for periods of day or more
- ``timezone``: IANA timezone (defaults to UTC)
- ``min_correctness_ratio``: minimum correct/total data to pass check
  (defaults to 0.9)

This service checks the timeseries data for outliers. Outliers are detected if
the minimum and/or maximum bounds are known and the amount of out of bounds data
on a given period is such that the correct/total ratio is lower than
``min_correctness_ratio``.

Download Weather Data Service
=============================

Task name: "DownloadWeatherDataScheduledTask"

Parameters;

- ``period``: one of ``"second"``, ``"minute"``, ``"hour"``, ``"day"``,
  ``"week"``, ``"month"``, ``"year"``
- ``period_multiplier``: integer multiplier, must be 1 for periods of day or more
- ``periods_before``: integer, number of period x multiplier
- ``periods_after``: integer, number of period x multiplier
- ``timezone``: IANA timezone (defaults to UTC)

This service downloads the weather data for all sites for which it is activated
and weather timeseries have been defined. The data is downloaded for a time
range defined by a number of periods before and after execution time.
