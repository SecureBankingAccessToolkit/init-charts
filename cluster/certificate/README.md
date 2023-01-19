# namespace-setup
The purpose of this helm chart to create a namespace and do some initial setup so that the Secure API Gateway components can be deployed to it.

The initial setup that this chart is currently responsible for is fetching secrets from Google Secrets manager.

## SSL cert and private key
Each environment has its own SSL Certificate and Private key, these are used to terminate TLS at nginx.

We use this secret to limit our dependency on cert-manager. Our namespaces are created and destroyed frequently and cert-manager has the tendency of requesting new prod letsencrypt certificates which regularly gives us a 429 throttle for cert requests.

By default, the chart will use the dev-crt and dev-key secrets from secret manager, but this may be over-ridden usign the externalCert.certPrefix value (see below).

### Values

| Value                   | description                                                                                  | default           |
|-------------------------|----------------------------------------------------------------------------------------------|-------------------|
| externalCert.secretName | Name of the kubernetes secret to store the sslcert                                           | `sslcert`         |
| externalCert.projectId  | GCP project that stores the secrets                                                          | `example-project` |
| externalCert.certPrefix | The prefix of the cert to pull from google secrets manager . Use 'dev' to use the "dev" cert | dev               |


## IG Truststore Certificates

IG needs to be configured to trust different trusted directories, these directories may be hosting their own CA and therefore the certs for the CA needs to be added to the truststore used by IG

The ig-truststore-secret template can be configured to fetch a Google Secret which is a PEM containing the additional CA certs that we wish to trust.

| Value                          | Description                                                                                                                                              | Default           |
|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|
| ig.truststore.secretName       | Name of the kubernetes secret to store the truststore data in                                                                                            | ig-truststore-pem |
| ig.truststore.googleSecretName | Name of the Google Secret to fetch the contents of. This secret is expected to be in PEM format. It is expected to contain one or more X509 certificates | ig-truststore-pem |
| ig.truststore.fileName         | The file name that the secret value will have when it is mounted into a container                                                                        | ig-truststore.pem |


## Example installation

The following will create the `bohocode-cdk` namespace, and use the cluster/certificate chart to deploy a secret to sslcert in `bohocode-cdk` namespace. It will deploy the the secrets called `bohocode-crt` and `bohocode-key` from google secret manager to tls.crt and tls.key in the `sslcert` secret.

```
helm upgrade cdk cluster/certificate --install --namespace bohocode-cdk --create-namespace --set namespace=bohocode-cdk --set externalCert.projectId=sbat-dev --set externalCert.certPrefix=bohocode
```
