# Docker Stack Repository

This repository contains multiple Docker application setups, each managed via a dedicated `docker-compose.yml` file and optional `.env` configurations. It is designed for use with Ansible in my Proxmox-based homelab environment.

Missing `.env` files are injected from my vault during Ansible playbook execution.

Until I implement a proper CI/CD pipeline, updates are also handled through an Ansible playbook.

---

## Structure

Each subfolder in this repository represents a single service or application. A typical layout looks like this:

```
/apps/
├── app-name/
│   └── docker-compose.yml

/vault/
├── env/
│   └── app-name.env
```

This structure is important because Ansible uses it to match vault files with the corresponding Docker stacks.

---

## Usage

You can manually start any container group with Docker Compose:

```bash
cd app-name
docker compose up -d
```

Alternatively, Ansible can be used to automate deployment across multiple stacks (see below).

---

## Ansible Integration

This repository is intended to be used with an Ansible playbook that:

1. Pulls selected subdirectories from this Git repository using sparse checkout.
2. Copies `.env` files from a vault or template directory.
3. Executes `docker compose up -d` within each directory.

Example task snippet:

```yaml
- name: Deploy Docker stack
  ansible.builtin.shell: |
    docker compose --env-file .env up -d
  args:
    chdir: "/mnt/docker/{{ item }}"
  loop: "{{ docker_apps }}"
```

Make sure to:

- Define `docker_apps` as a list of folders to deploy.
- Provide the corresponding `.env` files via Ansible vault or templates.

This setup ensures reproducibility, modularity, and centralized configuration management.

---

## Environment Variables

Most stacks rely on `.env` files to define ports, image versions, volume paths, and credentials. Be sure to provide the appropriate `.env` file before deploying a stack.

---

## License

MIT – free to use, modify, and share.
