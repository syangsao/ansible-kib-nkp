Here’s a ready-to-run Ansible playbook that uses Konvoy Image Builder (KIB) to produce a vSphere image for Nutanix NKP based on Rocky Linux 9.1. It also includes an optional step (tagged base_os) to build the base OS vSphere template first, using D2iQ’s vSphere base-template tooling—useful if you don’t already have a Rocky 9.1 base template in vCenter.

KIB expects a vSphere base OS template to exist, then it builds the final DKP/NKP image from that via an image.yaml. The image.yaml fields and the konvoy-image build vsphere ... flow come straight from the DKP docs, and the Rocky 9.1 variant is officially documented. 

**How to run**

Optionally export vSphere creds to avoid plain-text in vars.yml:
```
export VSPHERE_SERVER=vcenter.lab.local
export VSPHERE_USERNAME=administrator@vsphere.local
export VSPHERE_PASSWORD='SuperSecret!'
```

Run the optional base OS build + KIB (full pipeline):
```
ansible-playbook build_nkp_vsphere_image.yml -e @vars.yml
```

If you already have the base OS template in vCenter:
```
ansible-playbook build_nkp_vsphere_image.yml -e @vars.yml -e skip_base_os_build=true
```

After KIB finishes, it writes a manifest JSON containing artifact_id, which for vSphere is the template name created in your vCenter. That’s the image you’ll reference when creating NKP clusters. (The DKP doc shows this exact behavior and JSON field.) 
