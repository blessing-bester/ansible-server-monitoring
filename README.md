# Ansible Server Monitoring with Prometheus & Grafana

This project provides a robust, automated solution for deploying a monitoring stack using Ansible. It installs:

* **Prometheus**: For collecting and storing metrics.
* **Grafana**: For visualizing metrics and creating dashboards.
* **Node Exporter**: An agent to expose hardware and OS metrics on target servers.

The playbook is designed to be idempotent, meaning you can run it multiple times without causing issues.

## Prerequisites

* Ansible installed on your control machine (`pip install ansible`) or use line apt install/ dnf install
* SSH access to all target servers with a user that has `sudo` privileges.
* Firewall rules configured to allow traffic on ports 9100 (Node Exporter), 9090 (Prometheus), and 3000 (Grafana) between the relevant hosts.

## How to Use

1. **Configure Your Inventory (`hosts` file):**
    Edit the `hosts` file and replace the example IP addresses with your actual server details.

# Ansible inventory file
[monitoring_servers]
prometheus-server   ansible_host=192.168.1.X
grafana-server      ansible_host=192.168.1.X


[monitored_hosts]
web-server-01       ansible_host=192.168.1.X
db-server-01        ansible_host=192.168.1.X
cache-server-01     ansible_host=192.168.1.X

[all:vars]
ansible_user= blessing-bester
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3

# End of Invenory file

3.  **Run the Playbook:**
    The `site.yml` playbook will handle everything, from installing the agents to configuring the services.

    ```bash
    ansible-playbook -i hosts site.yml --ask-become-pass
    ```

    The `--ask-become-pass` flag will prompt you for the `sudo` password for the remote user. If you are using SSH keys with a passwordless sudo setup, you can omit this flag.

4.  **Access Your Dashboards:**
    * **Prometheus UI**: Open your browser and navigate to `http://<prometheus-server-ip>:9090`
    * **Grafana UI**: Open your browser and navigate to `http://<grafana-server-ip>:3000`
        * The default login credentials are `admin`/`admin`. You will be prompted to change the password on the first login.

graph TD
    A[monitoring-project/]
    B[.gitignore]
    C[README.md]
    D[ansible.cfg]
    E[hosts]
    F[site.yml]
    G[group_vars/]
    H[roles/]

    A --- B
    A --- C
    A --- D
    A --- E
    A --- F
    A --- G
    A --- H

    G --- Ga[all.yml]

    H --- Ha[node_exporter/]
    H --- Hb[prometheus_server/]
    H --- Hc[grafana_server/]

    Ha --- Ha_task[tasks/]
    Ha --- Ha_temp[templates/]
    Hb --- Hb_task[tasks/]
    Hb --- Hb_temp[templates/]
    Hc --- Hc_task[tasks/]
    Hc --- Hc_hand[handlers/]

    Ha_task --- Ha_main[main.yml]
    Ha_temp --- Ha_service[node_exporter.service.j2]
    Hb_task --- Hb_main[main.yml]
    Hb_temp --- Hb_prom[prometheus.yml.j2]
    Hc_task --- Hc_main[main.yml]
    Hc_hand --- Hc_hand_main[main.yml]

    subgraph Project Root
        A
    end

    subgraph Inventory
        E
        G
    end

    subgraph Playbook
        F
    end

    subgraph Roles
        H
    end

    subgraph Node Exporter Role
        Ha
    end

    subgraph Prometheus Role
        Hb
    end

    subgraph Grafana Role
        Hc
    end

    style A fill:#f9f,stroke:#333,stroke-width:2px;
    style Ha fill:#f9f,stroke:#333,stroke-width:2px;
    style Hb fill:#f9f,stroke:#333,stroke-width:2px;
    style Hc fill:#f9f,stroke:#333,stroke-width:2px;