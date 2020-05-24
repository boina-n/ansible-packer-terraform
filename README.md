# ansible-packer-terraform

Builds a docker image containing:
  
  * ansible   v2.5.1
  * awscli    v1.18.19
  * packer    v1.5.4
  * terraform v0.12.23

## Building Docker Image
```bash
docker build -t codebarber/ansible-packer-terraform .
```

## Examples getting versions

```bash
docker run --rm codebarber/ansible-packer-terraform terraform -version
docker run --rm codebarber/ansible-packer-terraform packer -version
docker run --rm codebarber/ansible-packer-terraform ansible --version
docker run --rm codebarber/ansible-packer-terraform aws --version
```

## Bash Functions
Here's example functions you can add to your profile (.bashrc or .bash_profile).  This creates shortcuts to quicly run commands with your current directory mapped as working directory in the docker image.  It also maps your .ssh and .aws folders to share keys and configs that the command might need.

I'm adding the prefix d to the beginning of the command to indicate its a docker image version of the command.  If you do not have these command on your host, you can easily drop the d.

```bash
dansible () 
{
  docker run --rm \
             -w /opt \
             -v $(pwd):/opt/ \
             -v ~/.aws:/root/.aws \
             -v ~/.ssh:/root/.ssh \
             codebarber/ansible-packer-terraform ansible $@
}

daws () 
{
  docker run --rm \
             -w /opt \
             -v $(pwd):/opt/ \
             -v ~/.aws:/root/.aws \
             -v ~/.ssh:/root/.ssh \
             codebarber/ansible-packer-terraform aws $@
}

dpacker () 
{
  docker run --rm \
             -w /opt \
             -v $(pwd):/opt/ \
             -v ~/.aws:/root/.aws \
             -v ~/.ssh:/root/.ssh \
             codebarber/ansible-packer-terraform packer $@
}

dterraform () 
{
  source ~/.rcs/ovh.rc
  sudo docker run --rm \
             -w /opt \
             -v $(pwd):/opt/ \
             -v ~/.aws:/root/.aws \
             -v ~/.ssh:/root/.ssh \
             --env OS_AUTH_URL=$OS_AUTH_URL \
             --env OS_PROJECT_ID=$OS_AUTH_URL \
             --env OS_PROJECT_NAME=$OS_PROJECT_NAME \
             --env OS_USER_DOMAIN_NAME=$OS_USER_DOMAIN_NAME \
             --env OS_PROJECT_DOMAIN_ID=$OS_PROJECT_DOMAIN_ID \
             --env OS_USERNAME=$OS_USERNAME \
             --env OS_PASSWORD=$OS_PASSWORD \
             --env OS_REGION_NAME=$OS_REGION_NAME \
             --env OS_INTERFACE=$OS_INTERFACE \
             --env OS_IDENTITY_API_VERSION=$OS_IDENTITY_API_VERSION \
             codebarber/ansible-packer-terraform terraform $@
}
```

Example usage:
```
$ dpacker -version
1.5.4

$ dansible --version
ansible 2.5.1
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.17 (default, Nov  7 2019, 10:07:09) [GCC 7.4.0]
```

## Development Notes

Under this section, I'm going to keep some notes here that might help others learning how to create docker images.

Initial size:
```
base: bionic-20200219
packages: 280
codebarber/ansible-packer-terraform   latest   880MB
Notes: ansible is default ubuntu package.
       packer/terraform are binary and awscli is pip install
```
