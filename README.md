# Ansible Role Jenkins Restore

A simple role to restore a Jenkins instance from an encrypted TAR backup files (S3).
The idea here is to install the desired Jenkins version via a package manager (takes care of service and user) and then simple extract the TAR and override JEKNIS_HOME.

due to memory and performance issues some Ansible Modules were dropped. (Trying to avoid running commands through shell)
Ansible rsync module is still in use but performance might be a bit slow due to using it.

### Defaults

```yaml
jenkins_home: '/var/lib/jenkins'

local_absolute_file_path:

s3_bucket_name: "{{ s3_prefix }}-jenkins-backup-prod-dr"
s3_object_name:
s3_prefix: 'vim'

aws_access_key: "{{ lookup('env','AWS_ACCESS_KEY_ID') }}"
aws_secret_key: "{{ lookup('env','AWS_SECRET_ACCESS_KEY') }}"
aws_region: "{{ lookup('env','AWS_DEFAULT_REGION') }}"

discover_s3_files: False

jenkins_user: 'jenkins'
jenkins_group: "{{ jenkins_user }}"
```

>**Errors tend to be generic, use -vvvv to debug. Usually it will be a permissions issue or a quoting or other key related issue'**

## Usage Example

```shell
ansible-playbook -i localhost jenkins-restore-playbook.yml -e 'discover_s3_files=True' -e 'aws_access_key=KEY' -e 'aws_secret_key=SECRET' -e 'aws_region=us-east-1' -e 'env=prod-dr' -l 'jenkins'
```

```shell
ansible-playbook -i ./inventories/prod-dr --private-key '~/.ssh/ansible-prod' jenkins-restore-playbook.yml -e 'discover_s3_files=True' -e 'aws_access_key=KEY' -e 'aws_secret_key=SECRET' -e 'env=prod-dr' -e 'aws_region=us-east-1' -l 'jenkins'
```