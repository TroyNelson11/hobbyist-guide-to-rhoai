# 1. Add an administrative user

<p align="center">
<a href="/docs/00-prerequisite.md">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="/docs/02-enable-gpu-support.md">Next</a>
</p>

### Objectives

- Creating an admin user, with cluster-admin RB using HTpasswd as the IdP.

### Rationale

- Only users with cluster administrator privileges can install and configure RHOAI.

### Takeaways

- Customers care about RBAC and IdP for their data science users and tools; not all ISVs integrate (i.e. Run.ai)
- kubeadmin should not be used as a best practice
- You can patch the CRB
- Once you have a new account delete kubeadmin
- RHOAI stipulates 'Access to the cluster as a user with the cluster-admin role; the kubeadmin user is not allowed.'

> You may be logged into the cluster as user `kubeadmin`, which is an automatically generated temporary user that should not be used as a best practice.

For this bootcamp, we are using HTpasswd as the Identity Provider (IdP). To learn more about supported IDPs refer [Here](https://docs.redhat.com/en/documentation/openshift_container_platform/4.15/html/authentication_and_authorization/understanding-identity-provider#supported-identity-providers).

![](/assets/user-auth.gif)

## Steps

> [!IMPORTANT]
> Ensure that you replace `<username>` and `<password>` with your own username and password in the following command.

- [ ] Create an htpasswd using a container on OpenShift

      oc run \
        --image httpd \
        -q --rm -i minion -- /bin/sh -c 'sleep 2; htpasswd -n -b -B -C10 trnelson AIWINGS4' > scratch/users.htpasswd

- [ ] Verify `htpasswd` file

      cat scratch/users.htpasswd

- [ ] Create a secret to represent the htpasswd file

      oc create secret generic htpasswd-local --from-file=htpasswd=scratch/users.htpasswd -n openshift-config

- [ ] Verify you created a `secret/htpasswd-local` object in `openshift-config` project

      oc get secret/htpasswd-local -n openshift-config

- [ ] Apply the resource to the default OAuth configuration to add the identity provider

      oc apply -f configs/01/htpasswd-cr.yaml

- [ ] Verify the identity provider

      oc get oauth/cluster -o yaml

- [ ] Watch for the cluster operator to cycle

      oc get co authentication -w

> [!WARNING]
> Ensure that you wait for the authentication ClusterOperator to become degraded, and then show Available with the "SINCE" column in a period of time related to you applying the OAuth configuration.

![](/assets/01-add-admin-since-col.png)

- [ ] As kubeadmin, assign the cluster-admin role to perform administrator level tasks

      oc adm policy add-cluster-role-to-user cluster-admin trnelson
> [!NOTE]
> You may see a line saying `Warning: User '<username>' not found`. This is expected and okay. The `User` object that derives from the login with your `htpasswd` credentials isn't created until the first time that user attempts to log in, and the policy will apply to that `User` object the first time it's created.

> Expected output
>
> `clusterrole.rbac.authorization.k8s.io/cluster-admin added: "<username>"`

- [ ] Log in to the cluster as a user from your identity provider, entering the password when prompted.

      oc login -u trnelson -p AIWINGS4

> [!NOTE]
> The remainder of the procedure should be completed with the new cluster-admin `<username>`.  

## Validation

![](/assets/01-validation.gif)


<p align="center">
<a href="/docs/00-prerequisite.md">Prev</a>
&nbsp;&nbsp;&nbsp;
<a href="/docs/02-enable-gpu-support.md">Next</a>
</p>
