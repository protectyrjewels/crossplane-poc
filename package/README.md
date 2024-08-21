# Store Crossplane Package

```sh
# Create a management Kubernetes cluster manually (e.g., minikube, Rancher Desktop, eksctl, etc.)

helm repo add crossplane-stable \
    https://charts.crossplane.io/stable

helm repo update

helm upgrade --install crossplane crossplane-stable/crossplane \
    --namespace crossplane-system --create-namespace --wait

# Replace `[...]` with your access key ID`
export AWS_ACCESS_KEY_ID=[...]

# Replace `[...]` with your secret access key
export AWS_SECRET_ACCESS_KEY=[...]

echo "[default]
aws_access_key_id = $AWS_ACCESS_KEY_ID
aws_secret_access_key = $AWS_SECRET_ACCESS_KEY
" > aws-creds.conf

kubectl --namespace crossplane-system \
    create secret generic aws-creds \
    --from-file creds=./aws-creds.conf

# Install the Composite Resource Definition (XRD)
# https://docs.crossplane.io/latest/concepts/composite-resource-definitions/
kubectl apply -n crossplane-system -f package/definition.yml

# Running the following command will list all the installed XRDs:
# $ kubectl get xrd
# NAME                     ESTABLISHED   OFFERED   AGE
# stores.adolfosilva.dev   True          True      2m25s

# Install the ProviderConfig
kubectl apply -n crossplane-system -f providers/00-aws-provider-config.yml
# providerconfig.aws.upbound.io/default created

# Install the necessary providers
kubectl apply -n crossplane-system -f providers/01-aws-s3-provider.yml
kubectl apply -n crossplane-system -f providers/02-aws-sqs-provider.yml
# provider.pkg.crossplane.io/provider-aws-s3 created
# provider.pkg.crossplane.io/provider-aws-sqs created

# You can list the installed providers with:
kubectl get providers

# You should now see two new Pods in the crossplane-system namespace related to the AWS provider installed.

# Install the store-composition Composition
kubectl apply -n crossplane-system -f package/composition.yml
# composition.apiextensions.crossplane.io/store-composition created

# You can list the available compositions with:
# $ kubectl get compositions
# NAME                XR-KIND   XR-APIVERSION              AGE
# store-composition   Store     adolfosilva.dev/v1alpha1   3m5s

# Finally, you can now create some stores:
kubectl apply -f examples/store-a81b90.yml
# store.adolfosilva.dev/store-a81b90 created

# You should be able to list all the created managed resources:
kubectl get managed

# Or, if be more specific in your listing:
kubectl get bucket,queue
# NAME           SYNCED   READY   EXTERNAL-NAME   AGE
# store-a81b90   True     False   store-a81b90    9m36s

# Delete the created store with:
kubectl delete -f examples/store-a81b90.yml
```
