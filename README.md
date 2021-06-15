# Kafdrop Helm Chart for Openshift Service mesh

## Chart Details
- Install Openshift service mesh compatible Kafdrop to view Kafka messages and topic metadata. Auth info to connect to a Broker and schema registry is not included in the Helm chart.
- This is a fork of [Kafdrop helm|https://github.com/obsidiandynamics/kafdrop/tree/master/chart] that will work in an Openshift and Openshift service mesh platform
- Openshift RBAC is enabled for kafdrop components
- kafdrop connects to a specific Kafka service account- Install Kafdrop chart for each environment
- Avro is the default message format. Switch between JSON and AVRO when viewing messages

## Kafdrop up and running
- Identify the namespace to install Kafdrop
- Determine if Kafdrop should run in a service mesh or not. By default, service mesh deployment is ON.
  - To turn OFF service mesh and instead pick route based access
    - set `route.enabled: true` and set
    ```yaml
    serviceMesh:
      route: false
      sidecar: false
    ```
- Request Devops to create a secret called `kafdrop-kafka`. Refer the below template
    - KAFKA_PROPERTIES_FILE_PATH can be a path like _/tmp/kafka-props/kafka.properties_
    - KAFKA_SASL_PROPERTIES should include at a minimum security.protocol, sasl.mechanism and sasl.jaas.config
    ```yaml
    kind: Secret
    apiVersion: v1
    type: Opaque
    metadata:
      name: kafdrop-kafka
    labels:
      app: kafdrop
    data:
      KAFKA_BOOTSTRAP_SERVERS: 
      KAFKA_PROPERTIES_FILE_PATH: 
      KAFKA_SASL_PROPERTIES: 
      KAFKA_SCHEMA_REGISTRY_BASIC_AUTH_USER_INFO: 
      KAFKA_SCHEMA_REGISTRY_URL: 
      KAFKA_SECURITY_PROTOCOL: 
    ```
- Identify the gateway hostname. Follow the format _[CHART_NAME].[NAMESPACE].[HOST_NAME]]_
- Install helm chart:
```bash
helm repo update
helm install kafdrop . -f values.yaml --set hostname=kafdrop-myhost -n anoop-dev
```
- After Kafdrop pod is up, fire up a browser and navigate to `https://{{ template "chart.fullname" . }}.{{ .Release.Namespace }}.myhost/`
    - Example: _https://kafdrop-anoop-dev.myhost/_
- Navigate to each topic and view topic, consumer metadata, view messages
