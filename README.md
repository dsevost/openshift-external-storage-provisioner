#
#
#

OpenShift using:

1. Build from scratch

oc new-project external-storage-provisioner-builder

oc new-build \\
    --name=golang-s2i \\
    https://github.com/dsevost/golang-s2i

oc new-build \
    --name=external-storage \
    -e DEBUG=1 \
    -e GO_GLOBAL_PROJECT_NAME=github.com/kubernetes-incubator/external-storage \
    -e TARGETS='nfs/cmd/nfs-provisioner iscsi/targetd' \
    golang-s2i~https://github.com/kubernetes-incubator/external-storage

oc new-build \
    --name=nfs-base-image \
    fedora:27~https://github.com/dsevost/openshift-external-storage-provisioner \
    --context-dir=nfs \
    --strategy=docker

oc new-build \
    --name=nfs-provisioner \
    --source-image=external-storage \
    --source-image-path=/opt/app-root/src/go/bin/nfs-provisioner:. \
    --dockerfile=$'FROM nfs-base-image\nENTRYPOINT ["/nfs-provisioner"]\nCOPY nfs-provisioner /'

oc policy add-role-to-user \
    system:image-puller system:serviceaccount:external-storage-provisioner:deployer

2. Build from templates

oc new-project external-storage-provisioner-builder

oc create -f https://raw.githubusercontent.com/dsevost/golang-s2i/master/golang-s2i-bc.yaml

oc create -f https://raw.githubusercontent.com/dsevost/openshift-external-storage-provisioner/master/external-storage-builder.yaml


3. Running provisioner

oc whoami

Run from cluster admin role

oc create -f https://raw.githubusercontent.com/dsevost/openshift-external-storage-provisioner/master/cluster-objects.yaml

oc adm new-project external-storage-provisioner --node-selector='' --admin=<output of previously oc whoami>

oc adm policy add-scc-to-user nfs-provisioner -z nfs-provisioner -n external-storage-provisioner

oc adm policy add-cluster-role-to-user nfs-provisioner-runner -z nfs-provisioner -n external-storage-provisioner


Switch back to ordinary user

oc project openshift-external-storage-provisioner

oc create -f https://raw.githubusercontent.com/dsevost/openshift-external-storage-provisioner/master/external-storage.yaml
