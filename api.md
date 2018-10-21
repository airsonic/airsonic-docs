---
layout: docs
title: API
permalink: /docs/api/
---
#### Subsonic API

We will try not to break with the Subsonic API.

Airsonic currently embed Subsonic API version 1.15.0.

> **NOTE**: Some features where removed or not integreted, so some endpoint of the API will return some errors:
- getChatMessages returns 410 Gone
- addChatMessage returns 410 Gone
- getVideoInfo returns 501 Not implemented
- getCaptions returns 501 Not implemented

If you want to use the API please find the [API documentation on the Subsonic website](http://www.subsonic.org/pages/api.jsp).
