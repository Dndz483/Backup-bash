# Backup-bash

Script en Bash para crear copias de seguridad comprimidas de un directorio o ruta de origen.

## ¿Qué hace?

El script:

- valida que se hayan pasado uno o dos argumentos:
  - `ORIGEN` (ruta que se va a respaldar)
  - `DIRECTORIO_BACKUPS` (opcional, carpeta donde se guardarán las copias)
- comprueba que las herramientas necesarias estén instaladas: `realpath`, `tar`, `date` y `sha256sum`
- resuelve la ruta de origen y crea la carpeta de backups si no existe
- evita que la carpeta de backups esté dentro del origen para no hacer copias recursivas infinitas
- genera un nombre único para el archivo `.tar.gz` con fecha y hora
- crea un archivo comprimido con `tar -czf`
- verifica que el archivo comprimido se pueda leer correctamente
- genera un archivo `.sha256` con el hash del respaldo
- comprueba la integridad del archivo usando `sha256sum --check`
- imprime la ruta del backup y del archivo de comprobación

## Requisitos

Debes tener instalado en el sistema:

- bash
- realpath
- tar
- date
- sha256sum

## Cómo ejecutarlo

1. Guarda el script con el nombre `Backup_test.sh`.
2. Dale permisos de ejecución:

```bash
chmod +x Backup_test.sh
```

3. Ejecuta el script:

```bash
./Backup_test.sh /ruta/de/origen
```

Para guardar la copia en una carpeta específica:

```bash
./Backup_test.sh /ruta/de/origen /ruta/de/backup
```

## Ejemplo

```bash
./Backup_test.sh /home/usuario/Documentos
```

Esto generará algo similar a:

```bash
/home/usuario/Documentos_20261015_153045.tar.gz
/home/usuario/Documentos_20261015_153045.tar.gz.sha256
```

si se usa la carpeta de backups por defecto `./backups`.

## Importante

- El primer argumento es obligatorio.
- El segundo argumento es opcional y sirve para indicar la carpeta donde se almacenarán los backups.
- Si la carpeta de backups está dentro del origen, el script rechazará la operación.

## Uso

```bash
Uso: Backup_test.sh ORIGEN [DIRECTORIO_BACKUPS]
```

Este script es útil para realizar copias de seguridad simples y verificadas, con un registro de integridad mediante SHA-256.
