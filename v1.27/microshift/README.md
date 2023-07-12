# MicroShift Container Platform by Red Hat

## Create a multi-node MicroShift cluster

1. Prepare two RHEL 9.2 systems suitable for running MicroShift as described as described in the first section of the [MicroShift installation instructions](https://access.redhat.com/documentation/en-us/red_hat_build_of_microshift/4.13/html/installing/microshift-install-rpm#microshift-install-system-requirements_microshift-install-rpm).
2. Add the MicroShift EC repo `sudo dnf config-manager --add-repo https://mirror.openshift.com/pub/openshift-v4/x86_64/microshift/ocp-dev-preview/4.14.0-ec.2/el9/os/`
3. Proceed to follow the remaining [installation instructions](https://access.redhat.com/documentation/en-us/red_hat_build_of_microshift/4.13/html/installing/microshift-install-rpm#installing-microshift-from-rpm-package_microshift-install-rpm) to install MicroShift to both systems, start the microshift service, access the microshift cluster locally, and confirm that a working MicroShift cluster is running on both systems.
4. Follow the [MicroShift multinode configuration instructions](https://github.com/openshift/microshift/blob/main/docs/contributor/multinode/setup.md) to reconfigure the two systems to operate as a single MicroShift cluster.

## Run conformance tests

1. By default OpenShift security rules do not allow running with privileged access.
   Below commands allow unprivileged users to run root level containers. Once
   conformance testing is completed, you should restore the default security rules.

```
oc adm policy add-scc-to-group privileged system:authenticated system:serviceaccounts
oc adm policy add-scc-to-group anyuid system:authenticated system:serviceaccounts
```

2. Follow the [test instructions](https://github.com/cncf/k8s-conformance/blob/master/instructions.md#running)
   to run the conformance tests. You will need to add the `--dns-namespace=openshift-dns`
   and `--dns-pod-labels=dns.operator.openshift.io/daemonset-dns=default`
   options so `sonobuoy` can find the cluster DNS pods:

```
sonobuoy run --mode=certified-conformance --dns-namespace=openshift-dns --dns-pod-labels=dns.operator.openshift.io/daemonset-dns=default
```

3. Once conformance testing is completed, restore the default security rules:

```
oc adm policy remove-scc-from-group anyuid system:authenticated system:serviceaccounts
oc adm policy remove-scc-from-group privileged system:authenticated system:serviceaccounts
```