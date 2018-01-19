Mattermost-kube
===

</br>

## How to deploy

Either clone this repo or use the raw url for each manifest file (eg. `https://raw.githubusercontent.com/BastienM/mattermost-kube/master/manifests/<file>`).

### Database

The current database setup is pretty straight forward.
</br>

Either you use an already existing one, in this case you can skip this setup. Or you can use the `db-*.yaml` included in this repo (in `mattermost-kube/manifests/`).

In the latter, edit the file `db-configmap.yaml` to adjust to your needs then simply use `kubectl` to deploy it :

```sh
$ kubectl create -f manifests/adb-configmap.yaml
$ kubectl create -f manifests/db-deployment.yaml
```

You can then check if everything went fine :

```sh
$ kubectl get po
NAME                              READY     STATUS    RESTARTS   AGE
mattermost-db-69cc7f8f76-2lmv6    1/1       Running   1          10s
```

If for some reasons the pod is stuck, you can get the detail with the following command :

```sh
$ kubectl describe po mattermost-db-<pod-id>
```

### Mattermost

> :warning: Using the **configMap** to pass a custom `config.json` file to the container makes the usage of environment variables useless and will mess with the database settings.
> </br>
> As such, I provide the said configMag but will not garantee its content

#### Standard deployment

Nothing fancy here.

```sh
$ kubectl create -f manifests/app-deployment.yaml
```

#### Custom configuration

If you will to be able to customize your mattermost instance beforehand, check and edit `app-configmap.yaml` accordingly.
</br>
And uncomment the matching parts in `app-deployment.yaml`.

```sh
$ kubectl create -f manifests/app-configmap.yaml
$ kubectl create -f manifests/app-deployment.yaml
```


### Using namespaces

You can leverage Kubernetes **namespace** in order to have multiple instances of Mattermost at the same time.

Two examples can be found in the folder `mattermost-kube/namespaces` and be deploy as any standard Kubernetes objects :

```sh
$ kubectl create -f namespaces/dev.json
$ kubectl create -f namespaces/prod.json
```

Alternatively you can use the flag `--namespace` at the end of `kubectl create` commands to dynamically create namespaces and deploy the objects directly to it at the same time.

## Tasks list (@todo)

* [ ] Implement [**HPA**](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (Horizontal Pod Autoscaler) for Mattermost
* [ ] Deploy Postgres as a cluster (HA mode) with [**Stolon**](https://github.com/sorintlab/stolon)
* [ ] Set up a cron'ed backup task for the Database using Kubernetes [`cronJob`](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) and `psql` 

