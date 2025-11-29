# 🚀 Infinite Money Glitch (PoC)

> **⚠️ DISCLAIMER / AVISO LEGAL:** > Este repositorio contiene código diseñado con fines **EDUCATIVOS** y de **PRUEBA DE CONCEPTO** sobre ingeniería social y scripts de automatización.  
> **No es un virus destructivo**, no roba datos, no encripta archivos ni daña el hardware.  
> El autor no se hace responsable del mal uso de este software. Ejecútalo solo en entornos controlados o con el consentimiento explícito del usuario (como una broma acordada).

---

## 📖 Sobre el Proyecto

Este proyecto es una demostración satírica de cómo funcionan las estafas de "dinero rápido" en internet, combinando una interfaz web atractiva con scripts de automatización persistentes.

El objetivo es demostrar cómo la **Ingeniería Social** (una web bonita y promesas falsas) puede convencer a un usuario de descargar y ejecutar código arbitrario en su máquina, saltándose las barreras de seguridad habituales mediante la confianza.

### Componentes

1.  **La Web (Frontend):**
    * Estética *Retro Wave / Cyberpunk*.
    * Simulación de terminal de hacking y mensajes persuasivos.
    * Detección de Sistema Operativo para ofrecer el payload correspondiente (`.exe` o `.zip`).

2.  **Los Scripts (Payloads):**
    * **Windows:** Un script Batch compilado a EXE que se auto-instala en el inicio.
    * **Linux:** Un script Bash híbrido que configura persistencia en el entorno de escritorio.
    * **Efectos:** Spam de ventanas inofensivas, reproducción de video y cambio de fondo de pantalla.

---

## 🛠️ Funcionamiento Técnico

### Windows
* **Persistencia:** Se copia a sí mismo en `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`.
* **Comportamiento:** Bucle infinito abriendo ventanas CMD.
* **PowerShell Injection:** Utiliza llamadas a la API de Windows para cambiar el fondo de pantalla sin necesidad de archivos externos.

### Linux
* **Persistencia:** Crea un archivo `.desktop` en `~/.config/autostart` y se oculta en `~/.hidden_sys_check`.
* **Compatibilidad:** Funciona en entornos basados en GNOME/XFCE (Ubuntu, Mint, Debian, Kali).
* **Ingeniería Social:** Requiere que el usuario otorgue permisos de ejecución manualmente (`chmod +x`), demostrando la barrera de seguridad de Linux.

---

## 🛑 GUÍA DE ELIMINACIÓN (CLEANUP)

Si has ejecutado este script y quieres detenerlo y eliminarlo por completo, sigue estos pasos según tu sistema operativo.

### 🪟 Para Windows

1.  **Detener el Caos:**
    * Presiona `Ctrl` + `Alt` + `Supr` y abre el **Administrador de Tareas**.
    * Busca "Procesador de comandos de Windows" o el nombre del `.exe`.
    * Haz clic derecho -> **Finalizar tarea**.
    * *(Alternativa: Crea un archivo llamado `killswitch.txt` en tu Escritorio).*

2.  **Eliminar Persistencia (Para que no vuelva al reiniciar):**
    * Presiona `Windows` + `R`.
    * Escribe `shell:startup` y pulsa Enter.
    * Borra el archivo **`security_daemon.bat`** (o el nombre del `.exe` generado).
    * Borra la carpeta `archivosSecretos` si aparece ahí.

3.  **Restaurar Fondo:**
    * Clic derecho en el escritorio -> Personalizar -> Fondo.

### 🐧 Para Linux

1.  **Detener el Script:**
    * Abre una terminal y ejecuta:
        ```bash
        killall security_check.sh
        killall xterm
        killall gnome-terminal-server
        ```
    * *(Alternativa: Crea un archivo llamado `killswitch.txt` en tu carpeta personal).*

2.  **Eliminar Persistencia:**
    * Borra el acceso directo de autoarranque:
        ```bash
        rm ~/.config/autostart/sys_check.desktop
        ```
    * Borra la carpeta oculta donde se instaló el script:
        ```bash
        rm -rf ~/.hidden_sys_check
        ```

3.  **Restaurar Fondo:**
    * Cambia el fondo desde la configuración de apariencia de tu distribución.

---

## 🔐 Kill Switch & Controles (Apagado de Emergencia)

Para garantizar la seguridad y el control durante la prueba, ambos scripts incluyen **dos mecanismos de parada** integrados que detienen el bucle de ventanas inmediatamente:

### 1. ⌨️ Método Interactivo (Tecla Q)
El script monitorea el teclado en tiempo real.
* **Instrucción:** Mantén presionada la tecla **`Q`** en cualquier momento durante la ejecución.
* **Resultado:** El bucle se rompe, se detiene la apertura de nuevas ventanas y el script finaliza (o pasa a la carga final, dependiendo de la configuración).

### 2. 📁 Método Pasivo (Archivo Bloqueo)
Ideal para detener el script si no tienes acceso al teclado o si se ejecuta en segundo plano. El script busca constantemente un archivo "llave".
* **Instrucción:** Crea un archivo vacío llamado **`killswitch.txt`**.
* **Ubicaciones detectadas:**
    * Escritorio
    * Documentos
    * Descargas
    * La misma carpeta donde está el script
    * Cualquier memoria USB conectada (D:, E:, /media/usb...)

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT - eres libre de usarlo y modificarlo, pero siempre bajo tu propia responsabilidad.
