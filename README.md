# Orquestación Cloud y Seguridad Profesional (K8s)
### ID de Repositorio: `fri231_T05_ms-orquestation-cloud`

---

##  Equipo de Trabajo
*   **Estudiante A (Infraestructura):** Freddy Sebastian Martin
*   **Estudiante B (Seguridad):** Luis Felipe Lévano

---

##  Arquitectura del Sistema
Se implementó un cluster de Kubernetes utilizando la distribución certificada **K3s** sobre instancias **Amazon EC2 (t3.medium)** en AWS Academy. Esta arquitectura garantiza alta disponibilidad y eficiencia operativa.

![Diagrama de Arquitectura](Arquitectura/Captura%20de%20pantalla%202026-05-06%20094737.png)

# Proceso de Implementación de Orquestación Cloud
## Fase 1: Infraestructura Base
- Provisionamiento de 2 instancias EC2 con Ubuntu 24.04 LTS.
- Instalación de K3s en el nodo Master y unión del nodo Worker mediante token de seguridad.
- Validación de alta disponibilidad con `kubectl get nodes`.
## Fase 2: Escalabilidad y Resiliencia
- Configuración de Horizontal Pod Autoscaler (HPA) para escalado dinámico (2-5 réplicas).
- Ejecución de un Rolling Update de la imagen `nginx:1.14.2` a `1.16.1` sin pérdida de servicio.
- Creación de un EBS Snapshot en la consola de AWS como estrategia de recuperación ante desastres.
## Fases 3, 4 y 5: Seguridad Avanzada
- **RBAC**: Creación de Roles y Bindings para restringir la eliminación de pods.
- **Network Policies**: Implementación de reglas para bloquear el tráfico no autorizado entre pods.
- **Escaneo**: Análisis de vulnerabilidades en imágenes de contenedores utilizando Trivy.
## Problemas y Soluciones (Análisis Técnico)
Este apartado detalla los desafíos encontrados durante el despliegue y cómo fueron superados:
| Problema | Causa Raíz | Solución Aplicada |
| --- | --- | --- |
| Restricción de Permisos IAM | AWS Academy bloquea la creación de roles necesarios para EKS gestionado. | Se migró a una arquitectura Self-Managed (EC2 + K3s), permitiendo control total del cluster. |
| Fallo de Comunicación Interna | Security Groups bloqueando puertos críticos (6443, 10250) entre nodos. | Se habilitó la regla "All Traffic" con origen en el ID del propio Security Group. |
| Métricas HPA `<unknown>` | El Metrics Server requiere ciclos de lectura iniciales y recursos definidos. | Se añadieron `resources.requests` en el Deployment y se esperó el tiempo de propagación (60s). |
| Error en Reglas de Seguridad | Uso de caracteres especiales (tildes) en las descripciones de reglas de AWS. | Se eliminaron tildes y caracteres no permitidos, estandarizando a formato alfanumérico simple. |
| Fallo en Network Policy | El motor de red no aplicaba las restricciones de tráfico inmediatamente. | Se verificó el servicio `kube-router` de K3s y se reiniciaron los pods para forzar la política. |
## Estructura del Proyecto
/infra: Scripts de instalación y configuración de nodos.
/yamls: Archivos de configuración (Deployments, RBAC, NetworkPolicies).
/evidencias: Capturas de pantalla de los comandos obligatorios de la rúbrica