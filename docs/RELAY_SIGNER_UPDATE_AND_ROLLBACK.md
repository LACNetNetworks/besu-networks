# Actualización y Rollback del Relay Signer

Este documento describe cómo actualizar y revertir la versión del Relay Signer de LACChain utilizando los playbooks de Ansible proporcionados.

## Requisitos

- Acceso SSH a la VM del nodo writer.
- Ansible instalado en la máquina local.
- Inventario de Ansible configurado correctamente.

## 🚀 Actualización del Relay Signer

El proceso de actualización descarga una versión específica del binario `gas-relay-signer` desde el repositorio de GitHub y lo instala, reemplazando la versión anterior. Automáticamente se crea un respaldo del binario anterior antes de la actualización.

### Playbook

- **Playbook principal**: `site-lacchain-update-relay-signer.yml`
- **Task de Ansible**: `roles/lacchain-writer-node/tasks/update-relay-signer.yaml`

### Cómo ejecutar la actualización

#### Opción 1: Actualización interactiva

```bash
# Ejecutar en un nodo específico
ansible-playbook -i inventory site-lacchain-update-relay-signer.yml --limit tu-nodo-writer
```

El sistema te preguntará qué versión/rama quieres instalar. Presiona Enter para usar el valor por defecto (`feature/nonce`) o escribe la versión deseada (ej: `v1.0.1`, `main`).

#### Opción 2: Especificar la versión directamente

```bash
# Actualizar a una rama específica
ansible-playbook -i inventory site-lacchain-update-relay-signer.yml -e relay_signer_version="feature/nonce"

# Actualizar a una versión específica
ansible-playbook -i inventory site-lacchain-update-relay-signer.yml -e relay_signer_version="v1.0.1"
```

### Modo de prueba (Dry-Run)

Para simular la actualización sin hacer cambios reales, usa la opción `--check`:

```bash
ansible-playbook -i inventory site-lacchain-update-relay-signer.yml --check
```

### Verificación post-actualización

1.  Verifica el estado del servicio:
    ```bash
    ansible -i inventory vm-test -m command -a "systemctl status relaysigner" --become
    ```

2.  Revisa los logs del servicio:
    ```bash
    ansible -i inventory vm-test -m command -a "journalctl -u relaysigner -n 20 --no-pager" --become
    ```

## ⏪ Rollback del Relay Signer

El proceso de rollback restaura la versión más reciente del binario del Relay Signer que fue respaldada automáticamente durante una actualización. El playbook busca archivos de respaldo (`gas-relay-signer.backup.*`), selecciona el más reciente y lo restaura.

### Playbook

- **Playbook principal**: `site-lacchain-rollback-relay-signer.yml`
- **Task de Ansible**: `roles/lacchain-writer-node/tasks/rollback-relay-signer.yaml`

### Cómo ejecutar el rollback

```bash
# Ejecutar en un nodo específico
ansible-playbook -i inventory site-lacchain-rollback-relay-signer.yml --limit tu-nodo-writer
```

El sistema te pedirá confirmación antes de proceder:

```
⚠️  Are you sure you want to rollback the Relay Signer? (yes/no) [no]:
```

Escribe `yes` y presiona Enter para continuar.

### Cómo funciona el rollback

1.  **Detección de respaldos**: El playbook busca archivos con el patrón `gas-relay-signer.backup.*` en el directorio `/root/lacchain/gas-relay-signer`.
2.  **Selección del más reciente**: Ordena los respaldos por fecha de modificación y selecciona el más nuevo.
3.  **Respaldo del binario actual**: Antes de restaurar, se crea un respaldo del binario actual con el nombre `gas-relay-signer.pre-rollback.<timestamp>` para mayor seguridad.
4.  **Restauración**: El binario del respaldo seleccionado se copia y reemplaza al binario actual.
5.  **Reinicio del servicio**: Se reinicia el servicio `relaysigner`.

### Verificación post-rollback

Usa los mismos comandos que para la verificación post-actualización para asegurarte de que el servicio esté funcionando correctamente.

