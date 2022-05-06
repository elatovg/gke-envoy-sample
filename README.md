# gke-envoy-sample

## Generate the Certificates

```bash
openssl req -x509 -newkey rsa:4096 -nodes -sha256 -days 365 \
    -keyout privkey.pem -out cert.pem -extensions san \
    -config \
    <(echo "[req]";
      echo distinguished_name=req;
      echo "[san]";
      echo subjectAltName=DNS:my.example.com
     ) \
    -subj '/CN=my.example.com'
```

Then import as a secret to kubernetes:

```bash
kubectl create secret tls envoy-certs --key=privkey.pem --cert=cert.pem \
    --dry-run=client --output=yaml | kubectl apply --filename -
```

If you want to you could create secret manifest files as well

```bash
kubectl create secret tls envoy-certs --key=privkey.pem --cert=cert.pem \
    --dry-run=client --output=yaml > secrets.yaml
```

And then apply them:

```bash
kubectl apply -f secrets.yaml
```

Lastly apply the rest of them:

```bash
> k apply -f .
configmap/envoy-conf created
deployment.apps/envoy created
secret/envoy-certs configured
service/envoy created
```