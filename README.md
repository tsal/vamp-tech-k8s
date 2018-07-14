# vamp-tech k8s

## Host config
### Digital Ocean
- master
  - size: s-3vcpu-1gb
- nodes:
  - size: s-1vcpu-2gb
  - 4 total

#### Setup

Create the hosts:

```bash
DROPLET_SLUG=example-k8s
IMAGE_SLUG=ubuntu-16-04-x64
DO_SSH_KEY=123456
DO_REGION=nyc3
MASTER_SIZE=s-3vcpu-1gb
NODE_SIZE=s-1vcpu-2gb

# Creates:
# - example-k8s-master
# - example-k8s-node01
# - example-k8s-node02
# - example-k8s-node03
# - example-k8s-node04
doctl compute droplet create ${DROPLET_SLUG}-master --region ${DO_REGION} --image ${IMAGE_SLUG} --size ${MASTER_SIZE} --enable-private-networking --ssh-keys ${DO_SSH_KEY}
doctl compute droplet create ${DROPLET_SLUG}-node{01,02,03,04} --region ${DO_REGION}  --image ${IMAGE_SLUG} --size ${NODE_SIZE} --enable-private-networking --ssh-keys ${DO_SSH_KEY}

# Get the host keys
for server in $(doctl compute droplet list --format Name,PublicIPv4 | grep ${DROPLET_SLUG} | awk 'print $2'); do
  ssh-keyscan $server
done
```

Execute the playbooks:

```bash
ansible-playbook host-init.yml
ansible-playbook kube-install.yml
ansible-playbook kube-init.yml
ansible-playbook worker-init.yml
# If you want to retrieve the kubeconfig after this point:
ansible-playbook get-kubeconfig.yml
```