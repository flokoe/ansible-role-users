# Ansible flokoe.users role

Ansible role to manage users, groups, authorized keys and sudo entries.

**Attention: This role is in development stage.**

**Why another ansible user role?**

There are many roles to manage users on Galaxy, so why write another one?  
Well, simply put, they did not satisfy my standards, were missing some features or were to complicated.

## Installation

Using `ansible-galaxy`:

```bash
ansible-galaxy install flokoe.users
```

Using `requirements.yml`:

```yaml
- src: flokoe.users
```

## Requirements

None

## Role Variables

### Example config

## Dependencies

None

## Example Playbook

```yaml
---

- name: Playbook
  hosts: all

  roles:
    - flokoe.users
```

## Contributing

In lieu of a formal style guide, take care to maintain the existing coding style and follow these general guidelines:

1. Fork it
2. Create your topic branch
3. Write and add proper tests
4. Add and commit your new functionality or fixes
5. Push to Github
6. Create new Pull Request

### Branches

Choosing a proper name for a branch helps us identify its purpose and possibly find an associated bug or feature. Generally a branch name should include a topic such as fix or feature followed by a description and an issue number if applicable. Branches should have only changes relevant to a specific issue.

```bash
git checkout -b fix/service-template-typo-1234
git checkout -b feature/config-handling-1235
```

## Testing

If you add new functionality, make sure to write appropriate tests as well.

To install all required test dependencies in a local development environment you can execute the following:

```bash
# If not existent
python3 -m venv env

source env/bin/activate

pip3 install --upgrade -r requirements.txt
```

Run all tests:

```bash
molecule test
```

## Author Information

Florian Köhler

## License

This project is under the MIT License. See the [LICENSE](LICENSE) file for the full license text.
