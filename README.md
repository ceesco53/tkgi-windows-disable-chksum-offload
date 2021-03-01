# Tanzu Kubernetes Grid Integrated (TKGI) / PKS Disable Checksum Offloading

## What does this do?

For windows-workers in PKS and TKGI clusters, this disables Checksum Offloading

Pods to the kubernetes api cluster ip service seem to have an issue with checksum offloading in NSX-T.  These pods may fail to communicate with the kubernetes api.

Running `Disable-NetAdapterChecksumOffload -Name "Ethernet0" -TcpIPv4 -NoRestart` seems to fix this behavior.

## How do I install it?

1. Open a shell prompt on a BOSH CLI with access to your TKGI bosh director, such as Ops Manager.
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.    

e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/ceesco53/tkgi-windows-disable-chksum-offload && cd tkgi-windows-disable-chksum-offload
bosh create-release --force
bosh upload-release ./dev_releases/tkgi-windows-disable-chksum-offload/tkgi-windows-disable-chksum-offload-0+dev.1.yml 

```
4. Configure the addon from this repo
```
bosh -n update-config --name=tkgi-windows-disable-chksum-offload --type=runtime ./addon.yml
```
5. Update your TKGI clusters via the Ops Manager "Apply Pending Changes" button with the "Upgrade All Clusters" errand.   You may also use the `tkgi upgrade-cluster` command to apply this addon to any individual cluster.
