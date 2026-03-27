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


📖 La Historia
El equipo de IT de la empresa "Global-Tech" notó una degradación severa en el rendimiento del servidor de base de datos a las 10:20 AM. Al intentar acceder, el administrador del sistema descubrió que no podía entrar con sus credenciales habituales.

Minutos después, apareció un archivo en el escritorio del servidor: README_TO_DECRYPT.txt. El servidor había sido infectado con un Ransomware.

Tu misión como Analista SOC:
Has recibido un volcado de logs (ataque.csv) extraído de los firewalls y del sistema afectado justo antes de que el atacante cortara las conexiones. Debes usar Splunk para reconstruir la "Línea de Tiempo del Ataque" y responder a las 15 preguntas críticas para el informe forense.


🛠️ Cómo usar este Repositorio
Descarga el archivo ataque.csv.

Cárgalo en tu instancia de Splunk.

Accede a la https://mekzzz8.github.io/Splunk-Challenge-x4/

Compara tus resultados con las queries SPL documentadas abajo.


Guía del Investigador: Cómo resolver el Challenge
🔴 Fase 1: Infiltración (Acceso Inicial)
¿Qué IP interna fue el objetivo del escaneo inicial (puerto 445)?

Qué buscar: El puerto 445 es SMB (compartición de archivos). Los atacantes lo escanean para ver si hay carpetas compartidas vulnerables.

Campo en Splunk: Busca el valor en dest_ip donde el port sea 445.

¿Qué IP realizó ese escaneo de red (IP atacante inicial)?

Qué buscar: La fuente del escaneo. En redes locales, esto suele ser un dispositivo ya infectado que intenta propagarse.

Campo en Splunk: Mira el campo source_ip asociado al evento de network_scan.

¿Cuántos intentos fallidos de login (Brute Force) se detectaron?

Qué buscar: El "ruido" antes de la entrada. Los atacantes prueban contraseñas comunes hasta que una funciona.

Campo en Splunk: Filtra por action=login y status=fail. Cuenta cuántos eventos hay.

¿Qué IP externa logró el acceso SSH (Login Success)?

Qué buscar: La IP que finalmente "rompió" la puerta. Esta es la IP real del atacante en internet.

Campo en Splunk: Busca status=success y mira la source_ip.

¿Qué usuario fue el objetivo del compromiso?

Qué buscar: Qué cuenta usaron para entrar. Los atacantes suelen buscar cuentas con privilegios como admin, root o support.

Campo en Splunk: Revisa el campo user.

🟠 Fase 2: Persistencia (Preparando el terreno)
¿En qué puerto se detectó el acceso exitoso?

Qué buscar: El servicio que estaba expuesto. El puerto 22 es el estándar para SSH (administración remota).

Campo en Splunk: Mira el campo port.

¿Qué IP DNS fue consultada sospechosamente?

Qué buscar: Los atacantes suelen "pinguear" servidores DNS conocidos (como el de Google) para verificar que el servidor infectado tiene salida a internet.

Campo en Splunk: Busca event_type=dns_query.

¿Cuál es la IP del servidor externo donde se descargó el malware?

Qué buscar: El servidor de Command & Control (C2) del atacante donde aloja sus herramientas maliciosas.

Campo en Splunk: Busca un evento de download y mira la dest_ip externa.

¿Cuántos bytes tiene el archivo de malware?

Qué buscar: El peso del "payload". Saber el tamaño ayuda a identificar de qué tipo de virus se trata.

Campo en Splunk: Mira el campo bytes.

¿A qué hora exacta terminó la descarga del malware?

Qué buscar: La marca de tiempo. Es crucial para saber cuánto tiempo tuvo el atacante para preparar el virus antes de lanzarlo.

Campo en Splunk: Mira el timestamp del evento get_file.

🟡 Fase 3: Impacto (El Desastre)
¿Qué acción de sistema se detectó a las 10:18:45?

Qué buscar: El inicio del Ransomware. Es el momento en que el malware deja de estar "quieto" y empieza a destruir.

Campo en Splunk: Busca el evento que ocurre en esa hora exacta.

¿Cuántos bytes totales de datos fueron cifrados?

Qué buscar: El alcance del daño. Representa cuánta información de la empresa ha quedado secuestrada.

Campo en Splunk: Busca action=encrypt y suma los bytes.

¿Qué tipo de evento se registró a las 10:22:00?

Qué buscar: La salida de datos. Los atacantes roban los datos antes de cifrarlos para chantajear a la empresa.

Campo en Splunk: Busca el event_type en esa franja horaria.

¿Cuál fue el volumen de datos robados (Exfiltración)?

Qué buscar: La cantidad de información filtrada. Es vital para saber qué leyes de protección de datos se han incumplido.

Campo en Splunk: Mira los bytes del evento de upload.

Duración total del incidente en minutos:

Qué buscar: La velocidad del ataque. Cuanto más rápido es, menos tiempo tienen los sistemas de defensa automáticos para reaccionar.

Cálculo: Resta la hora del primer evento (10:00) a la del último (10:22).

# ALERTA SPOILER 

SOLUCIONES

Fase 1: El Acceso (Infiltración)
1 y 2. Identificación del Escaneo Inicial

Concepto: Todo ataque suele empezar con un "reconocimiento". El atacante busca puertas abiertas.

Cómo lo encontramos: Buscamos eventos donde el event_type sea network_scan. Al hacerlo, vemos que la IP 192.168.1.50 (Origen) está intentando conectar con la 192.168.1.10 (Destino) a través del puerto 445 (SMB).

Query: source="ataque.csv" event_type=network_scan | table source_ip, dest_ip, port

3. Detectando la Fuerza Bruta

Concepto: Si un usuario real se equivoca de contraseña, lo hace 1 o 2 veces. Si vemos muchos fallos seguidos, es un bot.

Cómo lo encontramos: Filtramos los eventos de login que han fallado. Al contar los registros, vemos que hay 2 intentos fallidos justo antes del acceso.

Query: source="ataque.csv" action=login status=fail | stats count

4 y 5. El Compromiso de la Cuenta

Concepto: Necesitamos saber quién entró y desde dónde.

Cómo lo encontramos: Buscamos el primer evento de login con éxito (status=success). Esto nos revela que la IP externa 185.220.101.5 logró entrar usando la cuenta del usuario admin.

Query: source="ataque.csv" action=login status=success | table _time, source_ip, user

🟠 Fase 2: El Movimiento (Preparación)
6. El Punto de Entrada

Concepto: ¿Qué servicio estaba mal protegido?

Cómo lo encontramos: Mirando el detalle del login exitoso, vemos que se realizó a través del puerto 22, lo que confirma que el vector de ataque fue el servicio SSH.

7. La Prueba de Conexión (DNS)

Concepto: Los atacantes verifican si tienen salida a internet antes de descargar sus herramientas.

Cómo lo encontramos: Buscamos consultas DNS. Vemos que el servidor comprometido consultó a 8.8.8.8, una técnica común para confirmar que el tráfico saliente no está bloqueado.

Query: source="ataque.csv" event_type=dns_query

8, 9 y 10. Descarga del Malware (Payload)

Concepto: Una vez dentro, el atacante descarga su "kit de herramientas" desde su servidor de Comando y Control (C2).

Cómo lo encontramos: Buscamos el evento download. Identificamos que a las 10:12:30, el servidor descargó un archivo de 1,500,000 bytes desde la IP 91.189.91.43.

Query: source="ataque.csv" event_type=download | table _time, dest_ip, bytes

🟡 Fase 3: El Impacto (Ejecución y Robo)
11 y 12. Cifrado de Archivos (Ransomware)

Concepto: El malware empieza a transformar los archivos legítimos en archivos cifrados ilegibles.

Cómo lo encontramos: Buscamos la acción encrypt. Al sumar el campo bytes de todos estos eventos, obtenemos un total de 25,000,000 bytes de datos perdidos.

Query: source="ataque.csv" action=encrypt | stats sum(bytes)

13 y 14. Exfiltración (El Robo de Datos)

Concepto: El atacante se lleva una copia de los datos antes de destruirlos (Doble Extorsión).

Cómo lo encontramos: Buscamos el tipo de evento exfiltration. Vemos una acción de upload a las 10:22:00 donde se enviaron 50,000,000 bytes fuera de nuestra red.

Query: source="ataque.csv" event_type=exfiltration | table _time, action, bytes

15. Tiempo de Respuesta

Concepto: ¿Cuánto tardó el atacante en completar su misión?

Cómo lo encontramos: Restamos el tiempo del primer log (10:00:01) al último (10:22:01). El resultado son 22 minutos. Un ataque extremadamente rápido que requiere respuestas automatizadas.

Query: source="ataque.csv" | stats min(_time) as inicio, max(_time) as fin | eval diff_minutos=(fin-inicio)/60

- - -
Copyright (c) 2026 Jose Ignacio Navarro

Licensed under the MIT License.
