# Loki-Grafana Integration with AWS STS
Documentation
1. https://github.com/openshift/enhancements/blob/master/enhancements/cluster-logging/loki-tokenized-auth-enablement.md
2. https://loki-operator.dev/docs/short_lived_tokens_authentication.md/
   
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
 4. Notice the role arn from the previous step install the Loki operator, and provide the role arn
 ```
 oc get pods -w -n openshift-operators-redhat
 ```
 5. Install the clusterlogging operator.
 ```
 oc get pods -w -n openshift-logging
 ```
-> Add the secret logging-loki-managed-credentials for the versions below OCP4.14.20 manually and for later versions it creates automatically.
```
oc create secret generic logging-loki-managed-credentials  \
    --from-literal=role_arn="${ROLE_ARN}" \
    --from-literal=bucketnames="${LOKI_BUCKET_NAME}" \
    --from-literal=region="${AWS_DEFAULT_REGION}"
```
 6. configure loki CR
 ```
 oc create -f loki.yaml
 ```
Note : if there is a warning for storage schema, use the most recent version
```
  storage:
    schemas:
      - effectiveDate: '2023-10-15'
        version: v13
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
 10. To enable the audit logs, configure the clusterlogforwarder object in openshift-logging namespace from the logging operator
 ```
 oc create -f clf.yaml
 ```
12. Once all the Loki pods are up, there are two ways to configure Grafana:

    a. Use deployment:
        - a.1 Create service account:
            ```
            oc create -f sa.yaml
            ```

        - a.2 Create role binding:
            ```
            oc create -f clusterrolebinding.yaml
            ```

        - a.3 Create ConfigMap for Grafana config:
            ```
            oc create -f configmap_Grafanaconfig.yaml
            ```

        - a.4 Create ConfigMap for the datasource:
            ```
            oc create -f configmap_datasource.yaml
            ```

        - a.5 Create service:
            ```
            oc create -f service.yaml
            ```

        - a.6 Create deployment and change the username for Grafana and service URL in the value of GATEWAY_ADDRESS:
            ```
            oc create -f deployment.yaml
            ```

        - a.7 Create route:
            ```
            oc create -f route.yaml
            ```

    b. Use Grafana operator:
        - b.1 Follow the link for more information:
            [https://cloud.redhat.com/experts/o11y/ocp-grafana/](https://cloud.redhat.com/experts/o11y/ocp-grafana/)

