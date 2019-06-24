# Ansible role: TLS Certificates from Vault

An Ansible role that fetches SSL/TLS certificates and private keys from a
[Hashicorp Vault](https://www.vaultproject.io/)
[KV secrets engine](https://www.vaultproject.io/docs/secrets/kv/index.html) and
stores them on a host's file system.


## Requirements

- **[hvac](https://pypi.org/project/hvac/)** - HashiCorp Vault API client for
Python
- Running **[Hashicorp Vault](https://www.vaultproject.io/)** instance

**Currently supported operating systems:**
- Debian 9
- Ubuntu 18.04


## Role Variables

```YAML
vault_url: "http://myvault:8200"
```
The URL to the running Vault service.

```YAML
vault_path: "secret/certificates"
```
The path to the folder of the KV secrets engine containing the certificate secrets.

```YAML
vault_token_string: "{{
  'token=' + VAULT_TOKEN if VAULT_TOKEN is defined and VAULT_TOKEN
  else 'token=' + vault_token if vault_token is defined and vault_token
  else ''
}}"
```
Vault token parameter that is passed to the
[hashi_vault](https://docs.ansible.com/ansible/latest/plugins/lookup/hashi_vault.html)
lookup plugin. It is not intended to change this variable.

```YAML
vault_token:
```
The Vault token for authentication within Vault. It is also possible to specify
the token within the `VAULT_TOKEN` environment variable.

```YAML
vault_secret_cert_keyname: "cert"
```
The name of the key that contains the certificate (public key) within the Vault secret.

```YAML
vault_secret_key_keyname: "key"
```
The name of the key that contains the private key within the Vault secret.

```YAML
cert_dest_dir: "/etc/ssl/private"
```
File path to the directory on the host where certificates will be stored.

```YAML
certificates:
```
List of secret names stored beneath `vault_path` containing fields for cert and private key.


## Dependencies

This role does not depend on any other role from the Ansible Galaxy.


## Example Playbook

```YAML
    - hosts: servers
      vars:
        vault_url: "http://myvault:8200"
        vault_path: "secret/certificates"
        certificates:
          - www.example.org
          - web1.example.org
      roles:
         - netresearch.certificates_from_vault
```
**Note:** It is assumed that the certificates are available as secrets in Vault
at `secret/certificates/www.example.org` and
`secret/certificates/web1.example.org`.


## Local testing
The preferred way of locally testing the role is to use Docker. You will have
to install Docker on your system.

For all our tests we use `test-kitchen` with
`InSpec`. To install test-kitchen for Ubuntu 18.04:
```bash
$ sudo apt install ruby ruby-dev
$ sudo gem install test-kitchen inspec kitchen-ansible kitchen-inspec kitchen-docker
```

Please pass a valid Vault token to kitchen to fetch certificates from from your
running Vault instance for testing:
```
$ export VAULT_TOKEN=s.abcdefghijklmn1234567890
```
Rename `tests/test_vars.yml.dist` to `tests/test_vars.yml` and customized the
variables to your needs.

For starting the tests on all machines, please run:
```bash
$ kitchen test
```

For development you can also run the test step-by-step for a particular OS:
```bash
# create vagrant boxes
$ kitchen create debian

# rollout Ansible config
$ kitchen converge debian

# start InSpec tests
$ kitchen verify debian

# login into vagrant box
$ kitchen login debian
```


## License

GNU Affero General Public License v3.0


## Author Information

[Norman Bestfleisch](https://github.com/Normo) | [Netresearch DTT GmbH](https://www.netresearch.de/)
