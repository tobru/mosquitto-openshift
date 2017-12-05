# OpenShift compatible Eclipse Mosquitto Docker image

"Eclipse Mosquitto is an open source implementation of a server for version
3.1 and 3.1.1 of the MQTT protocol. Main homepage: http://mosquitto.org/"

This image provides some enhancements to the official Mosquitto
Docker image [eclipse-mosquitto](https://hub.docker.com/_/eclipse-mosquitto/) so that
it runs perfectly on OpenShift:

* Correct permissions on data directories
* Configuration in ConfigMap
* Logfiles on stdout

## Usage

Instantiate the template using:

```
oc process -f https://raw.githubusercontent.com/tobru/mosquitto-openshift/master/openshift-template.yaml MQTT_WEBSOCKET_URL=mymqttwebsocket.mydomain.com | oc create -f -
```

## Configuration

The configuration for Mosquitto is saved as a [ConfigMap](https://docs.openshift.org/latest/dev_guide/configmaps.html).
To update the Mosquitto configuration just edit the ConfigMap using
`oc edit configmap mosquitto` and redeploy the application with
`oc deploy mosquitto --latest` (the is no trigger when updating a ConfigMap).

The configuration delivered with this template doesn't contain any security
configuration (no TLS connections, no authentication. It's strongly recommended
to configure TLS and authentication.

## Volumes

To have persistent data, a volume can be mounted to `/mosquitto/data`:

```
oc volume dc/mosquitto --add --mount-path=/mosquitto/data --claim-mode=ReadWriteOnce --claim-name=mosquitto --claim-size=5Gi --type=persistentVolumeClaim
```

## Service expose

To expose the MQTT TCP service to the world, the OpenShift router can't be used.
Please find more information about the different ways available under
[Getting Traffic into the Cluster](https://docs.openshift.org/latest/dev_guide/getting_traffic_into_cluster.html).

Mosquitto is also able to expose MQTT over Websockets. This should work with
the OpenShift router (not yet tested, but the templates provides a route for
MQTT over Websockets).

## Pull requests

Pull requests to the template and the Dockerfile are welcome!
