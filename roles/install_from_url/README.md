# install_from_url

## Overview

Downloads a file from a given URL, unarchives it, and runs a set of install commands to install it. If the install command succeeds, sets custom persistent facts in [`/etc/ansible/facts.d`](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html#facts-d-or-local-facts) to indicate that the the software is installed and specify which version is installed. If the version is subsequently changed, or if any files or directories included in the `install_from_url_creates` list are removed, the download and install steps will run again. Otherwise, a reinstall can be performed by setting `install_from_url_force_install` to `true`.

## Variables

### Required

- `install_from_url_name`: A unique name for the package
- `install_from_url_version`: The version of the package to install
- `install_from_url_download_url`: The URL to download the package from
- `install_from_url_checksum`: The checksum of the downloaded file (formatted as expected by the [`get_url` module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html))
- `install_from_url_install_cmd`: The command to run to install the package (runs relative to either the unarchived directory or the download directory if the downloaded file is not unarchived)

### Optional

- `install_from_url_unarchive`: Whether the downloaded file needs to be unarchived (defauls to `true`)
- `install_from_url_creates`: A list of files or directories that if missing, will cause the install to run again (defaults to none)
- `install_from_url_force_install`: Forces the install to be performed even if custom facts indicate it has run already (defaults to `false`)

## Example

Assuming you've installed the collection, you can use the role like this:

```yaml
---
- name: Install acme.sh
  ansible.builtin.include_role:
    name: brianreumere.util.install_from_url
  vars:
    install_from_url_name: acme.sh
    install_from_url_version: 3.0.7
    install_from_url_download_url: 'https://github.com/acmesh-official/acme.sh/archive/refs/tags/3.0.7.tar.gz'
    install_from_url_checksum: 'sha256:abd446d6bd45d0b44dca1dcbd931348797a3f82d1ed6fb171472eaf851a8d849'
    install_from_url_install_cmd: |
      ./acme.sh --install \
         --home /root/.acme.sh \
         --accountemail someone@example.net
    install_from_url_creates:
      - /root/.acme.sh
```
