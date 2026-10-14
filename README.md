# Backup-bash
[Backup_test.sh](https://github.com/user-attachments/files/33415299/Backup_test.sh)

#!/usr/bin/env bash
set -Eeuo pipefail

usage() {
    printf 'Uso: %s ORIGEN [DIRECTORIO_BACKUPS]\n' "${0##*/}" >&2
    exit 2
}

[[ $# -ge 1 && $# -le 2 ]] || usage

for command_name in realpath tar date sha256sum; do
    if ! command -v "$command_name" >/dev/null 2>&1; then
        printf 'Error: se necesita el comando "%s".\n' "$command_name" >&2
        exit 1
    fi
done

source_path=$(realpath -e -- "$1") || {
    printf 'Error: no existe el origen: %s\n' "$1" >&2
    exit 1
}

backup_dir=${2:-./backups}
mkdir -p -- "$backup_dir"
backup_dir=$(realpath -e -- "$backup_dir")

if [[ -d $source_path && ( $backup_dir == "$source_path" || $backup_dir == "$source_path"/* ) ]]; then
    printf 'Error: el directorio de backups no puede estar dentro del origen.\n' >&2
    exit 1
fi

source_name=${source_path##*/}
if [[ -z $source_name ]]; then
    source_name=root
    source_parent=/
else
    source_parent=${source_path%/*}
    [[ -n $source_parent ]] || source_parent=/
fi

timestamp=$(date '+%Y%m%d_%H%M%S')
archive="$backup_dir/${source_name}_${timestamp}.tar.gz"
counter=1
while [[ -e $archive ]]; do
    archive="$backup_dir/${source_name}_${timestamp}_$counter.tar.gz"
    ((counter += 1))
done

tar -czf "$archive" -C "$source_parent" -- "$source_name"
if ! tar -tzf "$archive" >/dev/null; then
    printf 'Error: el backup se creó, pero no pasó la comprobación del archivo.\n' >&2
    exit 1
fi

checksum_file="$archive.sha256"
sha256sum -- "$archive" > "$checksum_file"
sha256sum --check "$checksum_file"
printf 'Backup creado: %s\n' "$archive"
printf 'Comprobación SHA-256 guardada en: %s\n' "$checksum_file"
