## Argo Application definitions
This helm chart contains the [Argo Application definitions](https://argoproj.github.io/argo-cd/operator-manual/declarative-setup/#applications) of our securebanking services. They represent the services inside a kubernetes context and Argo will ensure they are in the desired state defined by our individual service helm charts.

An Application can be either a helm/kustomize chart that lives in git or a helm repository.

### Git
A git application must supply the following:

- `repoURL`: The Git repository URL
- `targetRevision`: Git branch
- `path`: path to the helm chart or kubernetes Manifest definitions.

If the git repository supplies a helm chart then parameters can be overridden. ArgoCD is designed to use the exact chart as declared in Git but you can override parameters which is useful for testing in other environments etc.

### helm
You can supply a helm chart directly from a helm repository, in this case the following definitions are required

- `repoUrl`: The helm repository
- `chart`: The helm chart name
- `targetRevision`: The helm chart version

See the [MongoDB](./templates/mongodb.yaml) Application manifest for an example
