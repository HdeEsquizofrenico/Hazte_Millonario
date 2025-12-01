# Infinite Money Glitch (PoC) - Ransomware Simulation Framework

> **AVISO LEGAL:** > Este repositorio contiene código diseñado exclusivamente con fines **EDUCATIVOS** y de **INVESTIGACIÓN EN CIBERSEGURIDAD**.  
> Esta herramienta es un simulador de ransomware no destructivo. No cifra el contenido real de los archivos (solo renombra), no exfiltra datos sensibles y no daña el hardware.  
> El autor no se hace responsable del uso indebido de este software. Su ejecución debe realizarse únicamente en entornos aislados (Sandboxes/VMs) o redes bajo autorización explícita.

---

## Descripción del Proyecto

Este proyecto es una Prueba de Concepto (PoC) multiplataforma (Windows/Linux) diseñada para simular el ciclo de vida de un ataque de ransomware. Su objetivo es evaluar la capacidad de detección de soluciones EDR/AV y la respuesta ante incidentes sin comprometer la integridad de los datos.

El framework automatiza la infección, el establecimiento de persistencia, la simulación de cifrado de archivos, la interrupción visual/auditiva y la comunicación con un servidor de Comando y Control (C2).

---

## Capacidades Técnicas

El software emula el comportamiento de amenazas persistentes mediante las siguientes técnicas:

### 1. Ejecución y Carga (Payload Delivery)
El sistema identifica el Sistema Operativo del host y despliega el script correspondiente (`.bat` para Windows, `.sh` para Linux).
* **Detección de Entorno:** Identificación automática de directorios de usuario críticos (Escritorio, Documentos, Imágenes) utilizando variables de entorno (`%USERPROFILE%`) o estándares XDG.
* **Filtro de Objetivos:** El script localiza y "bloquea" únicamente archivos con extensiones específicas de productividad y multimedia (`.pdf`, `.docx`, `.jpg`, etc.) para evitar la corrupción del sistema operativo.

### 2. Persistencia y Ocultación
El malware asegura su ejecución tras el reinicio del sistema mediante múltiples vectores:

**Windows:**
* **Registro del Sistema:** Inyección de claves en `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`.
* **Programador de Tareas:** Creación de tareas persistentes (`schtasks`) configuradas para ejecutarse al inicio de sesión (`onlogon`).
* **Ocultación:** Instalación de binarios y recursos en directorios ocultos dentro de `%LOCALAPPDATA%`.
* **Living Off The Land (LOLBAS):** Uso de `PowerShell` oculto para la gestión de procesos en segundo plano (audio/bucle).

**Linux:**
* **Cron Jobs:** Inyección de tareas `@reboot` en el `crontab` del usuario actual.
* **XDG Autostart:** Creación de archivos `.desktop` en `~/.config/autostart`.
* **Ocultación:** Operación desde directorios ocultos en el `HOME` del usuario.

### 3. Simulación de Impacto (Ransomware Behavior)
* **Cifrado Simulado:** Renombrado masivo de archivos añadiendo la extensión `.locked`. El proceso es reversible.
* **Saturación de Recursos:** Despliegue masivo de terminales (spam visual) para simular la pérdida de control del sistema.
* **Coacción Psicológica:** Alteración del fondo de escritorio y reproducción de alertas de audio en bucle mediante subprocesos en segundo plano.

### 4. Propagación Lateral (Solo Windows)
El script monitoriza las unidades de almacenamiento extraíble (D:, E:, F:, G:) en tiempo real. Al detectar una unidad, replica el ejecutable e intenta establecer métodos de ejecución automática para facilitar el movimiento lateral en una red física.

### 5. Comunicación C2 (Command & Control)
Implementación de beaconing HTTP mediante `curl`. El cliente reporta estados al servidor remoto:
* `INFECTED_START`: Inicio de la ejecución.
* `ACTIVE_RUNNING`: Heartbeat periódico.
* `USB_INFECTED`: Propagación exitosa.
* `TIMEOUT/KILLED`: Estado final de la simulación.

---

## Mecanismos de Control (Kill Switch)

Para detener la simulación de manera segura durante las pruebas, se han implementado interrupciones prioritarias:

1.  **Archivo de Control:** La creación de un archivo llamado `killswitch.txt` en el directorio de ejecución, Escritorio o raíz de un USB detiene el proceso inmediatamente.
2.  **Interrupción por Teclado:** La entrada de la tecla `Q` durante la fase de ejecución aborta el bucle principal.

En ambos casos, el sistema revierte los cambios (restauración de nombres de archivo) y termina los procesos de persistencia.

---

## Procedimiento de Desinfección

Dado que el script establece persistencia avanzada, eliminar el archivo original no es suficiente. Siga estos pasos para limpiar el sistema:

### Windows
1.  **Detener Procesos:** Finalizar procesos asociados a `cmd.exe`, `conhost.exe` o `powershell.exe` que estén ejecutando audio.
2.  **Archivos:** Eliminar el directorio `%LOCALAPPDATA%\SystemUpdateService`.
3.  **Registro:** Eliminar la clave `WindowsSecurityHealth` en `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`.
4.  **Tareas:** Eliminar la tarea programada "OneDrive Update Check" desde el Programador de Tareas o mediante CMD:
    ```cmd
    schtasks /delete /tn "OneDrive Update Check" /f
    ```

### Linux
1.  **Detener Procesos:** `killall security_check.sh` y detener procesos de audio (`aplay`/`paplay`).
2.  **Archivos:** Eliminar el directorio `~/.hidden_sys_check`.
3.  **Cron:** Editar crontab (`crontab -e`) y eliminar la línea correspondiente al script.
4.  **Autostart:** Eliminar el archivo `~/.config/autostart/sys_check.desktop`.
Autostart: Eliminar el archivo ~/.config/autostart/sys_check.desktop.
