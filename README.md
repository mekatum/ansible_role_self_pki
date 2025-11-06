
# Ansible role for generate self-signed CA and domain certificates

This Ansible role issues a self-signed root certificate and issues certificates for domains signed with this self-signed root certificate.

## Requirements

This role requires:
* Python library [**cryptography**](https://github.com/pyca/cryptography)
* Ansible collection [community.crypto collection](https://galaxy.ansible.com/ui/repo/published/community/crypto/)

## Role variables

These aren't all the variables. This is the minimum required for the role to function. You can see all available variables in the descriptions of the corresponding modules:

[openssl_privatekey_module](https://docs.ansible.com/ansible/latest/collections/community/crypto/openssl_privatekey_module.html)\
[openssl_csr_pipe_module](https://docs.ansible.com/ansible/latest/collections/community/crypto/openssl_csr_pipe_module.html)\
[x509_certificate_module](https://docs.ansible.com/ansible/latest/collections/community/crypto/x509_certificate_module.html)

### Ansible role variables table

List of all role variables available for overriding.

| Variable | Description | Required | Example/Default |
|----------|-------------|:--------:|-----------------|
| `self_pki_ca_certificate_path` | Path to the directory for CA certificate placement | yes | `/root/CA` |
| `self_pki_ca_certificate_owner` | Owner of the CA certificate file | yes | `root` |
| `self_pki_ca_certificate_name` | CA certificate file name | yes | `ca_certificate` |
| `self_pki_ca_certificate_common_name` | The commonName field of the certificate signing request subject | yes | `My_company LLC` |
| `self_pki_ca_certificate_basic_constraints_critical` | Should the basicConstraints extension be considered as critical. | yes | `true` |
| `self_pki_ca_certificate_basic_constraints` | Indicates basic constraints, such as if the certificate is a CA | yes | `["CA:TRUE", "pathlen:0"]` |
| `self_pki_ca_private_key_name` | CA private key file name | yes | `ca_private` |
| `self_pki_ca_private_key_path` | The path to the private key to use when signing the certificate signing request | yes | `/root/CA` |
| `self_pki_ca_private_key_passphrase` | The passphrase for the private key | no\But strongly recommended | `Supper$ecr3t` |
| `self_pki_ca_private_key_encrypt_type` | The algorithm used to generate the TLS/SSL private key | yes | `RSA` |
| `self_pki_ca_private_key_encrypt_size` | Size (in bits) of the TLS/SSL key to generate | yes | `4096` |
| `self_pki_ca_private_key_backup` | Name of backup file created | yes | `false` |
| `self_pki_ca_private_key_usage_critical` | Should the keyUsage extension be considered as critical | yes | `true` |
| `self_pki_ca_private_key_usage` | This defines the purpose (for example encipherment, signature, certificate signing) of the key contained in the certificate | yes | `["keyCertSign"]` |
| `self_pki_client_private_key_name` | Client private key file name | yes | `client_private` |
| `self_pki_client_private_key_path` | Name of the file in which the generated TLS/SSL private key will be written. It will have 0600 mode if mode is not explicitly set | yes | `/root/certs` |
| `self_pki_client_certificate_name` | Client certificate file name | yes | `client_certificate` |
| `self_pki_client_certificate_path` | Path to the directory for client certificate placement | yes | `/root/certs` |
| `self_pki_client_certificate_owner` | Owner of the client certificate file | yes | `root` |
| `self_pki_client_certificate_ownca_not_after` | The point in time at which the certificate stops being valid\Time can be specified either as relative time or as absolute timestamp\Time will always be interpreted as UTC | yes | `+365d` |
| `self_pki_client_certificate_ownca_not_before` | The point in time the certificate is valid from\Time can be specified either as relative time or as absolute timestamp\Time will always be interpreted as UTC | yes | `-1d` |
| `self_pki_client_certificate_subject_alt_name` | Subject Alternative Name (SAN) extension to attach to the certificate signing request | yes | `["DNS:*.domain.lan", "DNS:domain.lan]`

## Notes

### Known issues


 1. This role is little bit non-idempotent. You will always have two changed tasks due to the use of the "openssl_csr_pipe_module" module.
If you wish, you can add a kludge as a construct [`changed_when: true`](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_error_handling.html#defining-changed) to suppress the actual status.

 2. This role only checks for the existence of generated certificates. This role does not verify the certificate contents. Therefore, if you need to revoke a certificate, reissue it, or issue a certificate with a new SAN, you must ensure that you do not have old certificate files.


### How to use

**_Keep private keys strictly confidential. Private keys should not leave the machine on which they were generated!_**

If you want to run a role as an unprivileged user, you must ensure that all file paths are writable by that user.

After generating the certificates (CA and domain certificates), you need to add the CA to your client's trust store (computer or phone), and specify the domain certificates in your application's settings (web-server or other).
This way, your client device will trust your services, and you will have encrypted connections with those services that support them.

## Examples

* Example playbook see in `docs/examples/playbooks/site.yml`
* Example inventory file see in `docs/examples/inventory.yml`

## Author information

This role is a wrapper around the code from the official Ansible instruction [How to create a small CA](https://docs.ansible.com/ansible/latest/collections/community/crypto/docsite/guide_ownca.html).
The role was written by [Max Muller](mailto:mekatum@gmail.com).

## License

This collection is primarily licensed and distributed as a whole under the GNU General Public License v3.0 or later.

See **LICENSE** for the full text.
