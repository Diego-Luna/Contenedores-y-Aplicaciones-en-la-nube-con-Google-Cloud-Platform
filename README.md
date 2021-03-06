# Curso-de-Contenedores-y-Aplicaciones-en-la-nube-con-Google-Cloud-Platform

## Introducción a contenedores

1. Máquinas Compartidas
   Los administradores de sistemas tenian que conseguir ejecutar varios servidores físicos.

Existen Dependencias aplicativas como:

- Kernel (requerido)
- Otras dependencias (opcionales)
- Runtimes del Lenguaje (Java, Node.js, etc.)
- Modulos del Kernel
- Herramientas del sistema
- Librerías de sistema
- Configuración

La manera antigua: Instalar aplicaciones en el host

- Múltiples aplicaciones por máquina
- Dependencias compartidas
- Beneficios:
- Ejecutables de tamaño pequeño
- Menos uso de almacenamiento y memoria
- Mayor utilización de recursos

Problemas

- Las dependencias se mezclaban unas con otras y con la máquina huésped
- Una aplicación puede acaparar recursos, impactando a otras
- Difícil de replicar y comúnmente existían diferencias
- entre ambientes (¡corre bien en desarrollo!)

La solución (temporal)

- Dedicar máquinas huésped para las aplicaciones críticas
- Empaquetar todas las dependencias con la aplicación

Problemas

- Dedicar máquinas huésped desperdiciaba recursos
- Máquinas físicas toman tiempo en aprovisionar y configurar

2. VMs/bare metal
   Se segmentaba una máquina en varias máquinas virtuales (Virtualización)

- Permitir que el hardware físico sea compartido entre aplicaciones
  como máquinas virtuales (VMs)
- Soluciones como Chef/Puppet/Ansible eran comúnmente utilizadas
  para administrar máquinas huésped y aplicativos
- VMs inmutables daban despliegues y rollbacks predecibles

Problemas

- Recursos desperdiciados (mucho overhead)
- Las VMs tardan mucho en iniciar

3.  Contenedores
    Mientras tanto en Google ños desarrolladores añadieron capacidades al kernel de Linux para soportar el aislamiento de procesos, poniendo las bases para la contenerización:

- Grupos de control (cgroups) --límites de recursos.
- Namespaces —aislamiento de procesos.
- Change Root (chroot) — aislamiento del sistema de archivos (ya existía).

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

- formato para construir imágenes de contenedores
- Imagen de contenedor Docker: binario empaquetado con un SO, sin kernel
- Docker conteiner: proceso aislado
- Conteiner registry: repositorio central de imágenes. Descarga

## Docker != Contenedores

- Es un formato para construir aplicaciones.
- Son imagenes que reciben cierto formato.
- Son contenedores que podemos instanciar usando alguna solución de orquestación (ejem. Swarm, Kubernetes).
- Existen Registros de contenedores para imagenes Google.

## Trabajando con contenedores

Listar las imágenes descargadas en nuestra máquina local.

```
docker images
```

Listar los contenedores que se están ejecutando en nuestra máquina.

```
docker ps
```

Listar los contenedores que se han ejecutado en nuestra máquina.

```
docker ps -a
```

**💡 Incluso podemos ver el status con el que terminaron en STATUS.**

Comando para ejecutar un contenedor.
El puerto lo podemos

```
-p puerto_mi_máquina:puerto_container.
```

Podemos usar `-d` para lanzarlo como un daemon (para que nos deje la consola “libre”).
Luego de la flag `--name` asignamos un nombre a nuestro contenedor., en este caso `app1`

```
docker run -d --name app1 -p 8080:80 tumtum/hello-world
```

Borrar imagenes que están en nuestra máquina.

```
docker rmi <id-imagen>

# docker rmi 563
```

**💡Podemos poner los primeros 3 dígitos del id para borrar una imagen.**
Para detener un contenedor que se está ejecutando.

```
docker stop <container-name>

# docker stop app1
```

Para construir una imagen.
La flag `-t` es para ponerle un tag/nombre a nuestra imagen.
El punto `.` es para indicar donde está el Dockerfile (en este caso significa que es en el directorio en el que estamos)

```
docker build -t user/nombre-de-mi-imagen .
```

Para ejecutar bash dentro de un contenedor que se está ejecutando.

```
docker exec -i -t <id-container> /bin/bash
```

## Piensa en Kubernetes como:

Una abstracción sobre la intraestructura. Ahora interactuamos con un plano de control para que ejecute las instrucciones que le damos de forma declarativa (es decir que busque el estado ideal).

- Escalamiento Vertical: Mas recursos a una carga de trabajo.
- Escalamiento Horizontal: Múltiples copias de la misma carga de trabajo.

## Componentes de los nodos

- kubelet

Un agente que se ejecuta en cada nodo del clúster. Se asegura de que los contenedores se ejecuten en un Pod.

kubelet toma un conjunto de PodSpecs que se proporcionan a través de varios mecanismos y asegura que los contenedores descritos en esas PodSpecs estén funcionando y en buen estado. El kubelet no administra contenedores que no fueron creados por Kubernetes.

- kube-proxy

kube-proxy es un proxy de red que se ejecuta en cada nodo de su clúster, implementando parte del concepto de servicio de Kubernetes.

kube-proxy mantiene las reglas de red en los nodos. Estas reglas de red permiten la comunicación de red con sus pods desde sesiones de red dentro o fuera de su clúster.

kube-proxy usa la capa de filtrado de paquetes del sistema operativo si hay una y está disponible. De lo contrario, kube-proxy reenvía el tráfico en sí.

- Container runtime

El tiempo de ejecución del contenedor es el software responsable de ejecutar los contenedores.

Kubernetes admite varios tiempos de ejecución de contenedores: Docker, containerd, CRI-O y cualquier implementación de Kubernetes CRI (Container Runtime Interface).

## Introducción a la malla de servicios (Service Mesh)

**¿Qué es una malla de servicio?**
Una malla de servicio provee una manera transparente e independiente al lenguaje para automatizar funciones de red de forma fácil y flexible

- Separa las aplicaciones del networking de las aplicaciones
- Desacopla la operación del desarrollo
- Provee un framework consistente para administrar la multi-nube

**¿Qué es Istio?**
Una malla de servicio. Pero aún más: es una plataforma de servicios abiertos para manejar las interacciones entre servicios entre contenedores y VMs

**Beneficios**

- Administra el flujo de tráfico de entrada, salida y dentro de tus servicios [Traffic splitting, canary, blue-green, mirroring…]

- Acceso y comunicación segura entre tus servicios usando políticas [mTLS,…]

- Visibilidad completa de todo lo que está pasando con tus servicios con instrumentación mínima

**¿Por qué habilitar una malla de servicio?**

1. Manera consistente de monitorear rendimiento, tasas de error y la postura de seguridad.
2. Te ayuda a construir servicios resilientes con despliegues seguros y failover zonal o regional.
3. Puedes tener seguridad consistente en tus servicios, incluyendo encriptación en tránsito, redes de cero confianza y protección de PII.

**Componentes funcionales de Istio**

- **Pilot**: Plano de control para configurar y mandar políticas de comunicación

- **Citadel**: Servicio-a-servicio auth[n,z] usando TLS mutuo, con manejo de identidades y credenciales incorporado

**La magia del sidecar**

- Desplegado con cada carga de trabajo
- Proxy de todo el tráfico de entrada y salida del servicio
- Direcciona tráfico (incluyendo reglas de enrutamiento
- Hace cumplir las políticas
- Reporta la telemetría
- Todo sin tener que embeber librerías de cliente
