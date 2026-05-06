# Este es el diseño final para tu archivo README.md. Está optimizado para ser conciso (cumpliendo la regla de máximo 2 páginas), profesional y con un énfasis especial en la sección de "Problemas y Soluciones", que es la que más valor tiene para tu calificación.

Orquestación Cloud y Seguridad Profesional (K8s)
ID de Repositorio: fri231_T05_ms-orquestation-cloud
Equipo de Trabajo
Estudiante A (Infraestructura): Freddy Sebastian Martin

Estudiante B (Seguridad): Luis Felipe Lévano

# Arquitectura del Sistema
Para este proyecto, se implementó un cluster de Kubernetes utilizando la distribución K3s sobre instancias Amazon EC2 (t3.medium). Esta solución garantiza alta disponibilidad y eficiencia en el entorno de AWS Academy.

graph TD
    subgraph AWS_Cloud [AWS Academy Lab]
        subgraph VPC [VPC: Kubernetes Cluster]
            M[k8s-master] -- "Orquestación" --> W[k8s-worker]
            W --> P1(mi-app pod-1)
            W --> P2(mi-app pod-2)
            W --> P3(mi-app pod-3)
            NP{fa:fa-shield NetPolicy} -.-> P1
        end
        S3[(S3: EBS Snapshot)]
        Trivy{{Trivy: Scan}}
        M -.-> S3
        Trivy -.-> W
    end
    User((DevOps)) -- "SSH / Kubectl" --> M

Proceso de Implementación
Fase 1: Infraestructura Base
Provisionamiento de 2 instancias EC2 con Ubuntu 24.04 LTS.

Instalación de K3s en el nodo Master y unión del nodo Worker mediante token de seguridad.

Validación de alta disponibilidad con kubectl get nodes.

Fase 2: Escalabilidad y Resiliencia
Configuración de Horizontal Pod Autoscaler (HPA) para escalado dinámico (2-5 réplicas).

Ejecución de un Rolling Update de la imagen nginx:1.14.2 a 1.16.1 sin pérdida de servicio.

Creación de un EBS Snapshot en la consola de AWS como estrategia de recuperación ante desastres.

Fase 3, 4 y 5: Seguridad Avanzada
RBAC: Creación de Roles y Bindings para restringir la eliminación de pods.

Network Policies: Implementación de reglas para bloquear el tráfico no autorizado entre pods.

Escaneo: Análisis de vulnerabilidades en imágenes de contenedores utilizando Trivy.
Problemas y Soluciones (Análisis Técnico)Este apartado detalla los desafíos encontrados durante el despliegue y cómo fueron superados:ProblemaCausa RaízSolución AplicadaRestricción de Permisos IAMAWS Academy bloquea la creación de roles necesarios para EKS gestionado.Se migró a una arquitectura Self-Managed (EC2 + K3s), permitiendo control total del cluster.Fallo de Comunicación InternaSecurity Groups bloqueando puertos críticos (6443, 10250) entre nodos.Se habilitó la regla "All Traffic" con origen en el ID del propio Security Group.Métricas HPA <unknown>El Metrics Server requiere ciclos de lectura iniciales y recursos definidos.Se añadieron resources.requests en el Deployment y se esperó el tiempo de propagación (60s).Error en Reglas de SeguridadUso de caracteres especiales (tildes) en las descripciones de reglas de AWS.Se eliminaron tildes y caracteres no permitidos, estandarizando a formato alfanumérico simple.Fallo en Network PolicyEl motor de red no aplicaba las restricciones de tráfico inmediatamente.Se verificó el servicio kube-router de K3s y se reiniciaron los   pods para forzar la política.
Estructura del Proyecto
/infra: Scripts de instalación y configuración de nodos.

/yamls: Archivos de configuración (Deployments, RBAC, NetworkPolicies).

/evidencias: Capturas de pantalla de los comandos obligatorios de la rúbrica.
