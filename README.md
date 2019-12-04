# ansible-ssl-certificates #

Use this role to create PEM-encoded client certificates using a certificate authority.

## Usage ##

Create your own ansible playbook with your own tasks, and include this role.  You will have to have this repository accessible within your playbook.

```yaml
- hosts: all:!localhost
  gather_facts: false
  become: true
  roles:
    - ssl-certificates
```

## Role Variables ##

```yaml
# the certificate authority key and certificate
certificate_authority_key: |
  -----BEGIN RSA PRIVATE KEY-----
  MIIEpAIBAAKCAQEAuyVYhPFaS4c1m37cmh+aHV0RYvbPIWUa4QirwnMer6V6HiQt
  ...
  PhRyE3ZutghvNmDdzLyWXLMAhGquyJ7o1rW5nZQAZmNlDhVQOw2V9g==
  -----END RSA PRIVATE KEY-----

certificate_authority_cert: |
  -----BEGIN CERTIFICATE-----
  MIIDbTCCAlWgAwIBAgIJAKFq5jxNvbBXMA0GCSqGSIb3DQEBCwUAME0xCzAJBgNV
  ....
  E38ivbPjL39CYqhx/CNzc4M=
  -----END CERTIFICATE-----

# the path to the directory in which to save the certificates
certificates_directory:  "/tmp"  # no trailing slash

# the file names of the certificate authority certificate files
certificate_authority_key_filename: 'cacert.key'
certificate_authority_cert_filename: 'cacert.pem'

# the filenames of the client certificates
client_key_filename: 'client.key'
client_cert_filename: 'client.pem'

# the validity period in days of the certificates
certificate_expiry_days: 3650

# certificate attributes
## take great care with this variable.  It should match the value used when
## generating the root certificate.  In case of problems try and set only the
## lower levels e.g. `/O=Some Company` only
## Also note that the common name (i.e. this part "/CN=example.com") needs to be
## different from the common name that was used when creating the certificate
## authority (CA) cert otherwise the certificate may not be validly signed
## see: https://stackoverflow.com/a/19738223
certificate_attributes: "/O=Some Company/OU=Some Department/CN=example.com"
```

You can create the `key` and `certificate` of a certificate authority in this way:

```sh
openssl genrsa -out MyRootCA.key 2048

openssl req -x509 -new -nodes -key MyRootCA.key -sha256 -days 3650 -out MyRootCA.pem
```

## Role Tasks ##

### main.yml ###

`main.yml` task copies the certificate authority key and cert to the `certificate_directory_path` directory and then uses them to create and configure the client certificate.

## License ##

Apache 2

## Authors ##

[Ona Engineering](https://ona.io)
