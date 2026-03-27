# Splunk-Challenge-x4
Este proyecto es un entorno de entrenamiento interactivo diseñado para simular un ataque real de Ransomware. El objetivo es que el usuario aprenda a utilizar Splunk Enterprise para investigar una intrusión desde su fase de reconocimiento hasta el impacto final.

## 🖥️ Arquitectura del Laboratorio: Entorno Windows Virtualizado
Para asegurar la máxima compatibilidad con las herramientas de análisis, se desplegó el SIEM sobre una **Máquina Virtual Windows** en Parallels Desktop.

### ⚙️ Especificaciones Técnicas
* **Hipervisor:** Parallels Desktop Pro (Optimizado para Apple Silicon M4).
* **SO Guest:** Windows 11 Pro (Arquitectura ARM con capa de emulación x64).
* **Software:** Splunk Enterprise v10.2.1 (Instalación vía MSI).

### 🛠️ Proceso de Despliegue
1. Instalación del paquete binario `.msi` con configuración de credenciales administrativas locales.
2. Configuración del servicio de Splunk para ejecución automática en segundo plano.
3. Ingesta de evidencias mediante el módulo de carga de datos estructurados (CSV).


<img width="1707" height="742" alt="image" src="https://github.com/user-attachments/assets/298e5d5f-c576-490b-9830-24e37d747a4d" /> imagen de splunk recien instalado en localhost

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4358a882-3635-420f-856f-ef3d7867a451" /> imagen de bloc notas con los rastrops del hacker

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bcdec005-93c3-42b2-bd10-40bdda42014e" /> imagen subiendo archivo .csv

<img width="1423" height="685" alt="image" src="https://github.com/user-attachments/assets/7943edfe-2591-4031-8dc4-70ebeba9b48a" /> imagen index.html para web del juego


