# ansible-k3

Provisionamiento automatizado de un **cluster K3s BETA** usando **Ansible**, con una arquitectura limpia, idempotente y escalable, alineada a buenas prácticas de **DevOps** y **GitOps**.

---

## 🎯 Objetivo del proyecto

Este repositorio tiene como objetivo **aprovisionar infraestructura base** para un cluster K3s:

* Bootstrap del sistema operativo
* Instalación del Control Plane
* Unión de nodos Worker
* Orquestación completa con un solo comando

> ⚠️ **Este proyecto NO gestiona aplicaciones, ingress, storage ni workloads.**
> Es únicamente para **aprovisionamiento del cluster**.

---

## 🧱 Alcance

### Incluye

* ✅ Bootstrap de nodos (timezone, paquetes base, swap)
* ✅ Instalación de K3s Control Plane
* ✅ Unión de nodos Worker
* ✅ Manejo seguro de tokens en runtime
* ✅ Inventarios por cliente
* ✅ Ejecución orquestada con `site.yml`
* ✅ Diseño idempotente y reproducible

### No incluye (por diseño)

* ❌ Ingress Controllers
* ❌ Storage (CSI, PVCs)
* ❌ Helm charts
* ❌ Deployments / Services
* ❌ GitOps tooling (ArgoCD, Flux)

Estos componentes deben vivir en **repositorios separados**.

---

## 📁 Estructura del proyecto

```
ansible-k3/
├── ansible.cfg
├── inventories/
│   ├── N70/
│   │   ├── hosts.ini
│   │   └── group_vars/
│   │       └── all.yml
│   └── PGJH/
├── playbooks/
│   ├── site.yml
│   ├── bootstrap.yml
│   ├── k3s-control-plane.yml
│   └── k3s-agents.yml
├── roles/
│   ├── common/
│   ├── k3s-control-plane/
│   └── k3s-agent/
└── README.md
```

---

## 🧩 Inventarios

Cada cliente tiene su propio inventario:

```
inventories/<CLIENTE>/
├── hosts.ini
└── group_vars/
    └── all.yml
```

### Ejemplo `hosts.ini`

```ini
[k3s_control_plane]
k3s-cp-1

[k3s_agents]
k3s-w-1
k3s-w-2

[storage]
storage-server
```

---

## 🔐 Variables globales

Definidas en:

```
inventories/<CLIENTE>/group_vars/all.yml
```

Ejemplo:

```yaml
k3s_version: "v1.34.3+k3s1"
timezone: "America/Mexico_City"
```

---

## ▶️ Uso

### Ejecutar todo el aprovisionamiento

```bash
ansible-playbook -i inventories/N70/hosts.ini playbooks/site.yml
```

Este comando:

1. Ejecuta el bootstrap en todos los nodos
2. Instala el Control Plane
3. Une los nodos Worker

Es **seguro ejecutar múltiples veces** (idempotente).

---

## 🧪 Validación

En el Control Plane:

```bash
kubectl get nodes
```

Resultado esperado:

```text
NAME       STATUS   ROLES           AGE   VERSION
k3s-cp-1   Ready    control-plane   ...   v1.34.3+k3s1
k3s-w-1    Ready    <none>          ...   v1.34.3+k3s1
k3s-w-2    Ready    <none>          ...   v1.34.3+k3s1
```

---

## 🧠 Decisiones de diseño

* 🔒 Los tokens de K3s se leen **en runtime**, no se almacenan
* 🧱 Separación estricta entre:

  * Aprovisionamiento (Ansible)
  * Configuración del cluster (GitOps / Helm)
* 🔁 Pensado para recreación completa de nodos
* 🚀 Escalable a HA en el futuro

---


## 📌 Estado del proyecto

**COMPLETADO para su alcance definido.**

Este repositorio puede reutilizarse para múltiples clientes.

---


