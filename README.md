# My Komodo Workspace

## Getting started with Komodo resources

This repository is structured as follows:

```sh
|_ .komodo
|    |_ gitops
|_ data
|_ data-apps
|    |_<Data apps code & deployment resources>
|_ jobs
|    |_<Jobs code & deployment resources>
|_ notebooks
|    |_<Jupyter notebooks>
|_ resources
     |_<your Komodo resources>
```

Let's see how to deploy Komodo resources on `Komodo Platform`:

- using the Dragon CLI
- by leveraging [GitOps](https://www.gitops.tech/) flow

### Deploy resources using Dragon CLI

The Dragon CLI acts as a gateway to the `Komodo Platform`. Store your resources under the [resources](resources) folder.

To deploy a particular resource:

```sh
dragon apply -f ./resources/<manifest>.yaml
```

To deploy all your resources at once:

```sh
dragon apply -f ./resources
```

> Trying to apply an already existing resources will trigger an error. You can use the `dragon update` command instead.

### GitOps flow: continuous and automatic deployment

> **Pre-requisites:**
>
> [Argo CD](https://argo-cd.readthedocs.io/en/stable/)

To enable GitOps on your Komodo resources, follow the steps below:

1) If not done already, go into project's directory and add/commit/push content to your GitHub repository

    ```sh
    git init
    git add -A
    git commit -m "Commit Komodo project"
    git branch -M main
    git remote add origin git@github.com:asaintsever/komodo-workspace.git
    git push -u origin main
    ```

2) If your git repository is a private one, let Argo CD knows about it

    Either using [Argo CD CLI](https://argo-cd.readthedocs.io/en/stable/getting_started/#2-download-argo-cd-cli):

    ```sh
    # Refer to command help for option flags: argocd repo add --help
    argocd repo add git@github.com:asaintsever/komodo-workspace.git [flags]
    ```

    Or directly in [Argo CD UI](https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/), going in `Settings` then `Repositories` and clicking on the `Connect Repo` button.

3) Create an Argo CD Application for your Komodo resources

    A ready to use Ago CD Application manifest is available under [gitops](.komodo/gitops/my.komodo.workspace.yaml) subfolder.

    You only need to deploy it into your Kubernetes cluster as a regular payload using `kubectl`:

    ```sh
    kubectl apply -f .komodo/gitops/my.komodo.workspace.yaml
    ```

4) Check status of your Ago CD Application

    Using CLI:

    ```sh
    argocd app get my.komodo.workspace
    ```

    > *In case an `Unauthenticated` error occurs, re-login with `argocd login` command.*

    Or by going into the `Applications` menu of Argo CD UI.

The Komodo resources stored under the [resources](resources) folder are then either automatically deployed (if you enabled the "GitOps Auto-sync" option during project creation) or just need an explicit sync from the UI or Argo CD CLI:

```sh
argocd app sync my.komodo.workspace
```
