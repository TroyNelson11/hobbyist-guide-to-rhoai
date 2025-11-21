# 0. Prerequisite

- [ ] Open a `bash` terminal on your local machine
- [ ] Git clone this repository

```sh
git clone https://github.com/redhat-na-ssa/hobbyist-guide-to-rhoai.git

cd hobbyist-guide-to-rhoai
```

- [ ] Create scratch directory

```sh
mkdir -p scratch
```

- [ ] Login to the cluster via terminal

```sh
oc login <openshift_cluster_url> -u <admin_username> -p <password>
```

- [ ] Run prerequisites (from this repository's root directory)

```sh
oc apply -f ./configs/00
```
```sh
oc apply -f ./configs/00/web-terminal-tooling.yaml
```

<p align="center">
<a href="/README.md">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="/docs/01-add-administrative-user.md">Next</a>
</p>
