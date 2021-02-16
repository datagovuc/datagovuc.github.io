---
title: Introduction
type: docs
---

# **`DOCUMENTACIÓN DE PROCESOS GOB D+I`**

Sitio pensado para depositar la documentación relativa a los procesos de la Subdirección de Tecnología del GOB D+I.

## **Cómo editar el sitio**

Si no tenemos el proyecto en nuestra máquina local, clonamos el repositorio:

```bash
git clone https://github.com/datagovuc/datagovuc.github.io.git
```
Una vez clonado el repo, abrimos el proyecto con algún editor de texto, de preferencia Visual Studio Code:

```bash
code .
```

Para editar el contenido, se debe seguir estándar que hay en el directorio `content` (que es donde está el contenido del sitio).

Una vez realizada la edición, se deben guardar los cambios y hacer un push al repo:

{{< tabs "uniqueid" >}}
{{< tab "Bash" >}} 
```bash
git add . && git commit -m "Nueva actualización" && git push
``` 
{{< /tab >}}
{{< tab "PowerShell" >}} 
```powershell
git add . ; git commit -m "Nueva actualización" ; git push
```
{{< /tab >}}
{{< /tabs >}}

Et voilà! El sitio está con integración continua, por lo que al hacer push quedará listo. Para ver los cambios hay que esperar algunos segundos y refrescar la página.

<br>

{{< hint info >}}
**Importante**  
Recordar que cada vez que se quiera editar el contenido, hay que hacer un `git pull` primero.
{{< /hint >}}