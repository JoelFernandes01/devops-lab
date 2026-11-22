# Roteiro de Aprendizado DevOps — Hands-On Infrastructure Lab

Este documento estabelece o roteiro prático e incremental de aprendizado DevOps, estruturado sob medida para a infraestrutura de laboratório nos ambientes **DEV (10.0.39.5)** e **PROD (10.0.39.7)**.

---

## 1. Visão Geral da Trilha de Aprendizado

A trilha é composta por 4 módulos práticos evolutivos, transitando do orquestrador Docker Swarm para Kubernetes HA, automação CI/CD, observabilidade e GitOps com Infraestrutura como Código (IaC).

```
[Módulo 1: Swarm & K8s Deploy] ──> [Módulo 2: Pipelines CI/CD] ──> [Módulo 3: Observabilidade] ──> [Módulo 4: IaC & GitOps]
```

---

## 2. Módulos do Roteiro

### Módulo 1: Orquestração & Deploy Prático (Docker Swarm & Kubernetes)
* **Objetivo:** Dominar a criação de aplicações multicamadas, redes overlay, persistência de dados e balanceamento de carga.
* **Projetos Práticos:**
  * Deploy de aplicações e serviços no Docker Swarm e Kubernetes HA.
  * Validação de persistência NFS dinâmico (`nfs-client`).
  * Migração declarativa de workloads via Portainer e ArgoCD.
* **Entregáveis:** Manifestos no repositório `devops-lab` (`docker-stack-dev.yml`, `k8s/apps/`).

### Módulo 2: Automação CI/CD (Continuous Integration & Delivery)
* **Objetivo:** Automatizar a criação de imagens de contêiner e o deploy contínuo.
* **Projetos Práticos:**
  * Execução de pipelines via Semaphore UI (Ansible) e Jenkins / GitHub Actions.
  * Análise de qualidade de código com SonarQube.
* **Entregáveis:** Playbooks Ansible, Jenkinsfile / Workflows GitHub.

### Módulo 3: Observabilidade & Telemetria (Prometheus, Grafana, Loki, Zabbix)
* **Objetivo:** Monitorar métricas dos hosts Proxmox, nós K8s/Swarm e centralizar logs.
* **Projetos Práticos:**
  * Coleta de métricas via Prometheus Operator, Grafana e Node Exporters.
  * Monitoramento de ativos via Zabbix.
  * Alertas via Alertmanager.
* **Entregáveis:** Dashboards Grafana e regras de alertas.

### Módulo 4: IaC & GitOps (Ansible, Terraform, ArgoCD, Portainer)
* **Objetivo:** Declarar e gerenciar 100% da infraestrutura e aplicações via repositório Git.
* **Projetos Práticos:**
  * Provisionamento de VMs no Proxmox via Terraform.
  * Configuração e hardening via Ansible / Semaphore.
  * Sincronização automatizada GitOps via ArgoCD e Portainer Stacks.
* **Entregáveis:** Repositório `https://github.com/JoelFernandes01/devops-lab`.

---

## 3. Comandos de Verificação & Diagnóstico da Infraestrutura

### 3.1. Diagnóstico de Recursos & Memória no Host (Docker Swarm / Proxmox)
* **Consumo Real de RAM (Host Linux vs Dashboard Proxmox):**
  ```bash
  free -h
  ```
  *Nota:* O Proxmox contabiliza memória em *buff/cache* do Linux como usada. O valor real disponível é indicado na coluna `available`.

* **Processos com Maior Consumo de RAM:**
  ```bash
  ps aux --sort=-%mem | head -n 10
  ```

* **Status do Agente Proxmox na VM:**
  ```bash
  systemctl status qemu-guest-agent | grep Active
  ```

### 3.2. Diagnóstico de Carga e Saúde do Cluster Docker Swarm
* **Métricas em Tempo Real dos Contêineres:**
  ```bash
  docker stats --no-stream
  ```

* **Saúde e Alocação das Tarefas nos Nós:**
  ```bash
  docker service ps $(docker service ls -q)
  ```

* **Status dos Nós do Swarm:**
  ```bash
  docker node ls
  ```

* **Logs e Limites do Kernel (SonarQube / Elasticsearch):**
  ```bash
  docker service logs --tail 50 sonarqube-stack_sonarqube
  sysctl vm.max_map_count
  ```

### 3.3. Verificação de Serviços, Armazenamento e Ingress no Kubernetes
* **StorageClasses e PVCs Ativos (NFS Dinâmico):**
  ```bash
  kubectl get sc,pvc -A
  ```

* **Pods de Observabilidade, CI/CD e Storage:**
  ```bash
  kubectl get pods -A | grep -iE "sonar|zabbix|prom|jenkins|nfs"
  ```

* **Serviços Expostos via LoadBalancer / Ingress (MetalLB / Traefik / Portainer):**
  ```bash
  kubectl get svc -A | grep LoadBalancer
  ```

---

## 4. Registro de Execução & Resolução de Problemas

| Módulo | Etapa / Diagnóstico | Status | Comandos Utilizados | Conclusão / Solução |
| :--- | :--- | :--- | :--- | :--- |
| **M1.1** | Verificação de RAM Swarm (10.0.39.31) | **Concluído** | `free -h` / `ps aux --sort=-%mem` | Consumo real de RAM normal (2.1GB em uso, 5.7GB em buff/cache). |
| **M1.2** | Diagnóstico SonarQube | **Concluído** | `sysctl vm.max_map_count` | `vm.max_map_count=1048576` OK. Serviço saudável no Swarm. |
| **M1.3** | Mapeamento NFS K8s | **Concluído** | `kubectl get sc,pvc -A` | StorageClass `nfs-client` ativo com PVCs de Zabbix e Prometheus. |
| **M1.4** | Repositório GitOps | **Concluído** | `git push -u origin main --force` | Repositório `JoelFernandes01/devops-lab` organizado e sincronizado. |
