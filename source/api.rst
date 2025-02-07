.. _api:

===
API
===

Data and features in BEMServer are exposed through a REST API. This API is used by
BEMServer UI web interface and is also meant to be used by third-party service to either
send or query data.

The input and output format is JSON. Some resources accept CSV as well: the resources
used to provide sites and timeseries descriptions and the resources used to post or get
timeseries data.

The API is documented using the OpenAPI format. By default, an interactive documentation
is exposed at the root URL of the API. The URL and visualization library may be modified
in the API settings.

Authentication
==============

The API uses JSON web token authentication (JWT). Basically, users use their credentials
(email and password) to get an access token and a refresh token. The access token is used
to access the API resources for a limited period of time (default: 15 minutes). When the
access token is expired, the refresh token may be used to get a new pair of tokens. When
the refresh token is expired (default: 60 days), the credentials must be used again.

.. note::

    User credentials for the API are the same than the ones used to connect to the UI. In
    fact, the UI uses them to authenticate to the API on behalf of the user.

.. note::

    There is no such feature as API keys in BEMServer (yet), so users developping a
    third-party service must configure it with their own personal credentials.

The default OpenAPI documentation viewer used to expose the API documentation has a
feature to use the API interactively, which comes in handy to check the authentication
and a few simple queries before actually producing any code.

The workflow can be summarized as follows (the resources paths are relative to the base
API path)::

    # Get access and refresh tokens
    # Pass credentials as parameters and get access and refresh token
    POST /auth/token JSON={"email": "user@company.com", "password": "xxx"}

    # When calling any resource, e.g. get users, send an Authorization header containing
    # the word Bearer followed by a space and the access token.
    # "Authorization": "Bearer {access_token}"
    GET /users headers={"Authorization": "Bearer {access_token}"}

    # When the access token is expired, use the refresh token to get a new pair of tokens
    POST /auth/token/refresh headers={"Authorization": "Bearer {refresh_token}"}

When a token token is expired, the API returns an authentication error (code 401), with
the payload `{"authentication": "expired_token"}`. Users may choose either to refresh
their tokens on a regular basis or on-the-fly when they get an error after the
expiration.


Resources
=========

The API documetation exposed by the API itself is the recommended source of information
about the API resources, since it is by definition synchronized with the API version. Any
other online documentation could be relative to another version of the API.
