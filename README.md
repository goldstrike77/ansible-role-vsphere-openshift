Adding vCenter root CA certificates to your system trust.

https://docs.openshift.com/container-platform/4.13/installing/installing_vsphere/installing-vsphere-installer-provisioned.html#installation-adding-vcenter-root-certificates_installing-vsphere-installer-provisioned


https://vsphere.local/certs/download.zip


Butane

DNS Reverse Lookup Zones



oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs --no-run-if-empty oc adm certificate approve