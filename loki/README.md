# Loki-Grafana Integration with AWS STS
### Steps
 1. Create the S3 object storage on AWS.
 2. Create the secret for s3 bucket to use it on the loki
 ```
 oc create -f logging-loki-s3.yaml
 ```
 3. Establish the Trust relationship and create the trust policy, role and attached the policy with the role. Trust policy will take the openshift-logging namespace and Service account will be $LOKISTACK_NAME and $LOKISTACK_NAME-ruler
 ```
 Create the role and apply the trust_policies.yaml and AllowFulls3access to the role"
 ```
 4. Notice the role arn from previous step and install the Loki operator and provide the role arn
 ```
 oc get pods -w -n openshift-operators-redhat
 ```
 5. Install the clusterlogging operator.
 ```
 oc get pods -w -n openshift-logging
 ```
 6. configure loki CR
 ```
 oc create -f loki.yaml
 ```
 7. configure Clusterlogging CR
 ```
 oc create -f logging.yaml
 ```
 8. Verify all the pods are running
 ```
 oc get pods -n openshift-logging
 ```
 9. Verify the logs from Console (Refresh Webconsole --> Observe --> Logs)
 10. once all the loki pods are up there are two ways to configure Grafana
  a. Use deployment
    a.1 create sa
    ```
    oc create -f sa.yaml
    ```
    a.2 create rolebinding 
    ```
    oc create -f clusterrolebinding.yaml
    ```
    a.3 create configmap for Grafana config.
    ```
    oc create -f Grafanaconfig.yaml
    ```
    a.4 create configmap for the datasource 
    ```
    oc create -f datasource.yaml
    ```
    a.5 create service 
    ```
    oc create -f service.yaml
    ```
    a.6 create deployment and change the username for grafana and service url in the value of GATEWAY_ADDRESS 
    ```
    oc create -f deployment.yaml
    ```
    a.7 create route 
    ```
    oc create -f route.yaml
    ```
  b. Use Grafana operator
     b.1 Follow the link for mobb ninja
     https://cloud.redhat.com/experts/o11y/ocp-grafana/



