
# Kubernetes

Comme vu précédemment `docker-compose` permet d'orchestrer des services,
mais seulement sur une seule machine hôte. La solution *de facto* pour
gérer des conteneurs sur plusieurs machines hôtes (cluster) est `Kubernetes`.

Kubernetes est formé de multiples composants interagissant ensemble :
- `kubelet`
- `apiserver`
- `scheduler`
- etc.

## Kubelet

kubelet est un agent installé sur chaque machine hôte. Il est responsable
du cycle de vie des conteneurs qui lui sont attribués.

### Installation et lancement en mode `Standalone`

Télécharger le binaire de `kubelet` :
```
wget https://storage.googleapis.com/kubernetes-release/release/v1.7.6/bin/linux/amd64/kubelet
chmod +x kubelet
```
Lancer `kubelet` :
```
mkdir manifests
sudo ./kubelet --pod-manifest-path $PWD/manifests
```
En fonctionnement normal `kubelet` a besoin de du composant `apiserver` pour obtenir 
les informations sur les conteneurs à déployer. Il est possible de le lancer en mode 
standalone, les descripteurs sont alors de simple fichiers locaux à la machine.

Le option `--pod-manifest-path` permet d'indiquer un répertoire qui contient les
descripteurs des conteneurs à lancer sur cette machine hôte.
Le service semble devoir fonctionner avec les droits root, d'où l'invocation avec `sudo`.

### Création de conteneurs

En fait avec Kubernetes, l'unité de déploiement n'est pas le conteneur mais le [`Pod`](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/).
Un Pod est un ensemble de conteneurs partageant les resources réseau et volume,
mais, sauf dans cas exceptionnels, on ne mettra qu'un seul conteneur par Pod.

Pour déployer un Pod, il faut le décrire dans un fichier YAML, puis placer le fichier
dans le répertoire `$PWD/manifests`.

Ecrire le fichier `nginx.yml` et le déplacer dans le répoire `manifests` :
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

Constater que le conteneur a été créé :
```
docker ps
```

## Apiserver

```
mkdir etcd-data
docker run -d \
    -v $PWD/etcd-data:/default.etcd \
    --net=host \
    --name=etcd-container \
    quay.io/coreos/etcd
```

```
export ETCDCTL_API=3
etcdctl get --prefix "" --keys-only
```

password.txt
```
secret,joe,1000,"users"
secret,john,1001,"users,admin"
```

```
./kube-apiserver \
  --etcd-servers=http://127.0.0.1:2379 \
  --service-cluster-ip-range=10.0.0.0/16 \
  --cert-dir=$PWD/certs \
  --authorization-mode=RBAC \
  --basic-auth-file=$PWD/password.txt

```

## Kubectl

## Scheduler

# Sources

* [Kubernetes High Level Overview](https://jvns.ca/blog/2017/06/04/learning-about-kubernetes/)

* [Kubernetes Components Part 1](http://kamalmarhubi.com/blog/2015/08/27/what-even-is-a-kubelet/)

* [Kubernetes Components Part 2](http://kamalmarhubi.com/blog/2015/09/06/kubernetes-from-the-ground-up-the-api-server/)

* [Kubernetes Components Part 3](http://kamalmarhubi.com/blog/2015/11/17/kubernetes-from-the-ground-up-the-scheduler/)
