# Curso-de-Contenedores-y-Aplicaciones-en-la-nube-con-Google-Cloud-Platform

## Introducción a contenedores

1. Máquinas Compartidas
Los administradores de sistemas tenian que conseguir ejecutar varios servidores físicos.

Existen Dependencias aplicativas como:

* Kernel (requerido)
* Otras dependencias (opcionales)
* Runtimes del Lenguaje (Java, Node.js, etc.)
* Modulos del Kernel
* Herramientas del sistema
* Librerías de sistema
* Configuración

La manera antigua: Instalar aplicaciones en el host

* Múltiples aplicaciones por máquina
* Dependencias compartidas
* Beneficios:
* Ejecutables de tamaño pequeño
* Menos uso de almacenamiento y memoria
* Mayor utilización de recursos

Problemas
* Las dependencias se mezclaban unas con otras y con la máquina huésped
* Una aplicación puede acaparar recursos, impactando a otras
* Difícil de replicar y comúnmente existían diferencias
* entre ambientes (¡corre bien en desarrollo!)

La solución (temporal)

* Dedicar máquinas huésped para las aplicaciones críticas
* Empaquetar todas las dependencias con la aplicación

Problemas
* Dedicar máquinas huésped desperdiciaba recursos
* Máquinas físicas toman tiempo en aprovisionar y configurar

2. VMs/bare metal
Se segmentaba una máquina en varias máquinas virtuales (Virtualización)

* Permitir que el hardware físico sea compartido entre aplicaciones
como máquinas virtuales (VMs)
* Soluciones como Chef/Puppet/Ansible eran comúnmente utilizadas
para administrar máquinas huésped y aplicativos
* VMs inmutables daban despliegues y rollbacks predecibles

Problemas
* Recursos desperdiciados (mucho overhead)
* Las VMs tardan mucho en iniciar

3.  Contenedores
Mientras tanto en Google ños desarrolladores añadieron capacidades al kernel de Linux para soportar el aislamiento de procesos, poniendo las bases para la contenerización:

* Grupos de control (cgroups) --límites de recursos.
* Namespaces —aislamiento de procesos.
* Change Root (chroot) — aislamiento del sistema de archivos (ya existía).

Esto permitió a las apps/procesos:

Correr con cpu y memoria asignada (específica)
Aislar de otros procesos
Proveer acceso limitado al sistema de archivos
La nueva manera es desplegar contenedores

Virtualización a nivel OS (namespaces, cgroups, …)

Aislado, entre otros procesos y el host

Lás imágenes de contenedor tienen su propio sistema de archivos, sus propios recursos y se ejecutan como su propio proceso
Rápido

Portable entre sistemas operativos y entre nubes

Siempre y cuando el kernel objetivo fuera el mismo que el de la máquina huésped
Ambientes consistentes entre desarrollo y producción

Creando imágenes inmutables durante el proceso de construcción, en lugar de durante el proceso de despliegue

## Introducción a Docker

* formato para construir imágenes de contenedores
* Imagen de contenedor Docker: binario empaquetado con un SO, sin kernel
* Docker conteiner: proceso aislado
* Conteiner registry: repositorio central de imágenes. Descarga