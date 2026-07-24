# Security

The OwnTracks Recorder is a simple program which has basically a single task which is to take location publishes from OwnTracks clients, perform optional geo-lookups, and store the data.

The program was initially an MQTT client only so protected by an MQTT broker. This broker (server) is the component which provides authentication, authorization, transport level security (TLS/SSL), and the Recorder "simply" subscribes to this broker at a particular MQTT topic branch and receives what the broker gives it.

In MQTT mode, the Recorder has no open incoming TCP ports -- it connects out to an MQTT broker (either TCP/1883 or TCP/8883, configurable) and optionally to a geo-location service (TCP/443, likewise configurable).

## HTTP

Roughly in 2015 we decided to offer HTTP for those people who weren't willing to stand up an MQTT broker, imagining that HTTP would be easier for them. Our apps got HTTP support as did the Recorder. But that meant that the Recorder needed incoming HTTP (TCP/80, configurable) for the apps to connect to and publish their location payloads.

This was implemented, and we explicitly did not implement authorization, authentication, or transport level security, making users aware that a HTTP proxy (e.g. nginx or Apache) ought to be used for implementing those capabilities.

Implementing HTTP caused a whole slew of additional issues we needed to resolve, e.g. support for Friends, special support for CARDs, etc. most of which we were able to address, even if they occasionally appear to be kludges, which in fact they frequently are.

Very specifically we need to warn Recorder users using HTTP:
- any client permitted to access the `/pub` endpoint can publish data to the Recorder
- any client permitted to access the `/api` endpoint can access any user's data, and even destroy it, depending on whether or not the Recorder was built with the `WITH_KILL` flag.
