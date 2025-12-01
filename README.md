# 🚀 Infinite Money Glitch (PoC) - Advanced Red Teaming Demo

> **⚠️ AVISO LEGAL Y ÉTICO:** > Este repositorio contiene código diseñado exclusivamente con fines **EDUCATIVOS** y de **INVESTIGACIÓN EN CIBERSEGURIDAD**.  
> El objetivo es demostrar vectores de ataque mediante Ingeniería Social, técnicas de persistencia avanzada y simulación de C2.  
> **No es un virus destructivo:** No cifra archivos, no roba credenciales y no daña el hardware.  
> El autor no se hace responsable del mal uso de estas herramientas. Ejecútalo únicamente en entornos controlados (Sandboxes/VMs) o con consentimiento explícito.

---

## 📖 Sobre el Proyecto

Este proyecto es una **Prueba de Concepto (PoC)** que simula un ataque de cadena completa (*Kill Chain*), desde el vector de entrada hasta el establecimiento de persistencia, propagación lateral y comunicación con un servidor de Comando y Control (C2).

Utiliza una fachada satírica ("Infinite Money Glitch") con estética *Cyberpunk* para engañar al usuario y lograr la ejecución de código, demostrando cómo la **Ingeniería Social** supera las barreras técnicas y cómo el malware moderno se oculta en el sistema.

---

## ⚙️ Arquitectura y Capacidades Técnicas

El malware simula el comportamiento de una amenaza persistente (APT) utilizando técnicas modernas:

### 1. 🦠 Vector de Infección (Ingeniería Social)
* **Web Cebo:** Interfaz HTML/CSS reactiva que detecta el Sistema Operativo del visitante.
* **Payload Adaptativo:** Entrega automática de `Hacer_Dinero.exe` (Windows) o `Hacer_Dinero.zip` (Linux).
* **Engaño Técnico:** Instrucciones falsas ("Drivers de Lamborghini", "Bypass de seguridad") para convencer al usuario de otorgar permisos de ejecución (`chmod +x` o `Run as Admin`).

### 2. ⚓ Persistencia Avanzada & Ocultación (Stealth)
A diferencia del malware básico, este script no usa la carpeta de "Inicio" visible. Sobrevive a reinicios ocultándose en el sistema:

#### 🪟 Windows (Advanced Persistence)
* **Doble Persistencia:**
    1.  **Registro:** Inyección en `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`.
    2.  **Task Scheduler:** Creación de tarea programada (`schtasks`) que se ejecuta al iniciar sesión (`onlogon`).
* **Stealth (Ocultación):** Los archivos se instalan en directorios ocultos del sistema (`%LOCALAPPDATA%\SystemUpdateService`).
* **Camuflaje de Recursos:** Los archivos multimedia (`video.mp4`, `fondo.jpg`) son renombrados automáticamente a archivos de sistema (`core_sys.dat`, `config_ui.jpg`) para evitar la detección visual por parte del usuario.
* **LOLBAS:** Uso de binarios nativos (`powershell.exe`, `reg.exe`, `schtasks.exe`) para operar sin levantar sospechas ("Living Off The Land").

#### 🐧 Linux (Advanced Persistence)
* **Cron Injection:** Inserción de tareas `@reboot` en el `crontab` del usuario (invisible en la carpeta de inicio estándar).
* **Stealth Dir:** Operación desde directorios ocultos (`~/.hidden_sys_check`) y uso de carpetas invisibles (`.data`) para los recursos.

### 3. 🔄 Propagación Lateral (USB Spreading)
El payload de Windows incluye capacidades de gusano (worm) limitadas:
* **Detección de Medios:** Escanea activamente unidades externas conectadas (D:, E:, F:, G:).
* **Replicación:** Si detecta una unidad USB, se copia a sí mismo bajo el nombre `Hacer_Dinero.exe` esperando que una futura víctima lo ejecute en otro equipo (Ingeniería Social física).

### 4. 📡 Command & Control (C2 Beaconing)
Implementación de comunicación unidireccional para monitorización de víctimas:
* **Heartbeat:** Los scripts envían "pings" periódicos mediante `curl` (POST requests) a un servidor remoto (Webhook).
* **Reporte de Estado:** Notifica eventos clave: `INFECTED`, `ACTIVE`, `USB_INFECTED`, `KILLED_BY_USER`, `PAYLOAD_EXECUTED`.
* **Infraestructura:** Compatible con Webhooks para monitorización en tiempo real sin necesidad de abrir puertos en el cliente.

---

## 🛠️ Instalación y Uso (Entorno de Prueba)

### Prerrequisitos
1.  **Servidor C2:** Configura una URL en [Webhook.site](https://webhook.site) y pégala en la variable `C2_URL` dentro de los scripts `installer.bat` e `installer.sh`.
2.  **Empaquetado:**
    * **Windows:** Empaqueta `installer.bat` y la carpeta de recursos como un archivo SFX auto-extraíble (`.exe`).
    * **Linux:** Comprime `installer.sh`, el `LEEME.txt` y la carpeta oculta `.data` en un `.zip`.

### Ejecución
1.  Abre el archivo `index.html` en tu navegador.
2.  Selecciona tu sistema operativo y descarga el archivo.
3.  Sigue las instrucciones de "instalación" (Ingeniería Social).
4.  Observa en tu panel de Webhook cómo la "víctima" se conecta y reporta estado.

---

## 🔐 Kill Switch (Mecanismos de Parada)

Para mantener el control durante las pruebas, se han implementado dos "botones de pánico":

1.  **Interactivo (Tecla Q):** Mantener presionada la tecla **`Q`** detiene el bucle de ventanas y envía una señal de "Abort" al C2 inmediatamente.
2.  **Pasivo (Archivo):** Crear un archivo llamado **`killswitch.txt`** en el Escritorio, Descargas o raíz de un USB detendrá el proceso automáticamente al ser detectado.

---

## 🧹 GUÍA DE LIMPIEZA TOTAL (Uninstall)

Debido a la persistencia avanzada, **borrar el archivo descargado NO detendrá el script** al reiniciar. Sigue estos pasos para desinfectar la máquina completamente:



### 🪟 Windows (Limpieza Profunda)
1.  **Detener Proceso:** Administrador de Tareas (`Ctrl+Alt+Supr`) -> Finalizar `cmd.exe` o `conhost.exe`.
2.  **Borrar Archivos Ocultos:** Eliminar la carpeta: `%LOCALAPPDATA%\SystemUpdateService`.
3.  **Limpiar Registro:** Ejecutar `regedit`, ir a `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run` y borrar la clave `WindowsSecurityHealth`.
4.  **Borrar Tarea Programada:** Abrir CMD y ejecutar: 
    ```cmd
    schtasks /delete /tn "OneDrive Update Check" /f
    ```

### 🐧 Linux (Limpieza Profunda)
1.  **Detener Proceso:** Ejecutar en terminal: `killall security_check.sh`.
2.  **Limpiar Cron:** Ejecutar `crontab -e` y borrar la línea que contiene `@reboot ... security_check.sh`.
3.  **Borrar Archivos:** ```bash
    rm -rf ~/.hidden_sys_check
    ```
4.  **Limpiar Autostart:** `rm ~/.config/autostart/sys_check.desktop`.

---

## 📄 Licencia
Distribuido bajo la licencia MIT. Prohibido su uso para actividades ilegales o maliciosas sin consentimiento.
