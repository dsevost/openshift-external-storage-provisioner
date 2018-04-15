#
#
#

OpenShift using:

oc new-project openshift-external-storage-provisioner

oc new-build \
    https://github.com/dsevost/golang-s2i

oc new-build \
    -e DEBUG=1 \
    -e GO_GLOBAL_PROJECT_NAME=github.com/kubernetes-incubator/external-storage \
    -e TARGETS='nfs/cmd/nfs-provisioner' \
    golang-s2i~https://github.com/kubernetes-incubator/external-storage
