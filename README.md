# Knative Pipeline Devex Demo

This repo shows a simple example pipeline for going from a git commit to running Knative Service.

## Setting up the pipeline

Assuming you have a Concourse setup:

		fly -t knative-poc \
    set-pipeline \
    --pipeline devex \
    --config pipeline.yml \
    --load-vars-from secrets.yaml

### `secrets.yaml`

Not included in this repository, for what I hope are obvious reason, is a `secrets.yaml` file. You will need to write one yourself. Unless your Concourse was configured with a credential store, in which case, use that.

You need to put these secrets into this file:

* `github-private-key`: This is the private key from an SSH keypair that you have added to your account.
* `gcr-json-key`: This is the JSON key used to identify a service account on your GCP Project.
* `gke-cluster-token`: Your bearer token from logging into GKE. Copy and paste from your kubeconfig.
* `gke-cluster-ca`: The cluster CA certificate. This is also in kubeconfig, but base64 encoded. You will need to decode it first.

If you check in a `secrets.yaml`, I accept no responsibility. I do however judge you. Don't do it.

### GCP accounts

I'm assuming GCP for everything here. GKE for Kubernetes, Google Container Registry, Google Cloud Storage.

You need to:

1. Set up a service account on GCP
1. Give that service account "Storage Admin" rights. You'd think narrower read/write permissions would work, but they didn't.
1. Create a GCS bucket. Be a good neighbour and give it a nice long name.

### Cleaning up

* Delete your `secrets.yaml`
* Destroy the service account.

## Appendix: Setting Up A Concourse

I'm using the [Helm chart for Concourse](https://github.com/kubernetes/charts/tree/master/stable/concourse), with some adjustments in `concourse-chart-values.yaml`. My command line is:

    helm install \
      stable/concourse \
    --replace \
    --name concourse \
    --namespace concourse \
    --values concourse-chart-values.yaml

**THIS IS NOT A SECURED CONCOURSE INSTALLATION. Do _NOT_ use it for production.**
