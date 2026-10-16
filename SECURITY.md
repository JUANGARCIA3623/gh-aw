#!/usr/bin/env python3
"""
euniprincez Digital v2026 — Instalador automatizado de Arduino CLI

titular de los Derechos: Juan Valentín garcía Espinoza
Identificador Legal: gaej940310hsprsn02 
ecosistema: SBE-GAEJ Core System
jurisdicción: San Luis Potosí, México

"""

import os
import subprocess
import shutil
import sys
import urllib.request

def instalar_arduino_cli():
    print("[*] Iniciando automatización de entorno para Euniprincez Digital v2026...")
    
    # 1. Descargar el script oficial de instalación de Arduino CLI
    install_url = "https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh"
    print("[*] Descargando script de instalación remoto...")
    
    try:
        with urllib.request.urlopen(install_url) as response:
            install_script = response.read().decode('utf-8')
        
        # Ejecutar el script descargado pasándoselo al intérprete 'sh'
        print("[*] Ejecutando script de instalación...")
        proceso = subprocess.run(['sh'], input=install_script, text=True, capture_output=True)
        
        if proceso.returncode != 0:
            print(f"[!] Error crítico en el instalador: {proceso.stderr}")
            sys.exit(1)
            
    except Exception as e:
        print(f"[!] Fallo de red o ejecución: {e}")
        sys.exit(1)

    # 2. Determinar la ruta global de comandos (Soporte nativo para Termux $PREFIX o Linux)
    prefix = os.environ.get('PREFIX')
    if prefix:
        destino_bin = os.path.join(prefix, 'bin')
    else:
        destino_bin = '/usr/local/bin'
        
    os.makedirs(destino_bin, exist_ok=True)
    
    origen_ejecutable = os.path.join('bin', 'arduino-cli')
    destino_ejecutable = os.path.join(destino_bin, 'arduino-cli')
    
    print(f"[*] Moviendo binario a la ruta global: {destino_ejecutable}")
    if os.path.exists(origen_ejecutable):
        shutil.move(origen_ejecutable, destino_ejecutable)
        # Otorgar permisos de ejecución explícitos (chmod +x)
        os.chmod(destino_ejecutable, 0o755)
    else:
        print("[!] No se encontró el ejecutable en la ruta temporal 'bin/'.")
        sys.exit(1)

    # 3. Limpieza segura de la carpeta temporal 'bin'
    print("[*] Limpiando archivos temporales...")
    if os.path.exists('bin'):
        shutil.rmtree('bin')

    # 4. Actualizar el índice de tarjetas e instalar el núcleo AVR (Uno, Nano, Mega)
    print("[*] Actualizando índice de núcleos en arduino-cli...")
    res_update = subprocess.run(['arduino-cli', 'core', 'update-index'], capture_output=True, text=True)
    if res_update.returncode != 0:
        print(f"[!] Advertencia al actualizar el índice: {res_update.stderr}")

    print("[*] Instalando núcleo arduino:avr...")
    res_install = subprocess.run(['arduino-cli', 'core', 'install', 'arduino:avr'], capture_output=True, text=True)
    if res_install.returncode != 0:
        print(f"[!] Error al instalar el núcleo AVR: {res_install.stderr}")
        sys.exit(1)

    print("\n[+] ¡Configuración completada con éxito!")
    print(f"[+] Propietario Registrado: Juan Valentín García Espinoza (ID: GAEJ940310HSPRSN02)")

if __name__ == '__main__':
    instalar_arduino_cli()



Thanks for helping make GitHub safe for everyone.

# Security

GitHub takes the security of our software products and services seriously, including all of the open source code repositories managed through our GitHub organizations, such as [GitHub](https://github.com/GitHub).

Even though [open source repositories are outside of the scope of our bug bounty program](https://bounty.github.com/index.html#scope) and therefore not eligible for bounty rewards, we will ensure that your finding gets passed along to the appropriate maintainers for remediation.

## Reporting Security Issues

If you believe you have found a security vulnerability in any GitHub-owned repository, please report it to us through coordinated disclosure.

**Please do not report security vulnerabilities through public GitHub issues, discussions, or pull requests.**

Instead, please send an email to opensource-security[@]github.com.

Please include as much of the information listed below as you can to help us better understand and resolve the issue:

- The type of issue (e.g., buffer overflow, SQL injection, or cross-site scripting)
- Full paths of source file(s) related to the manifestation of the issue
- The location of the affected source code (tag/branch/commit or direct URL)
- Any special configuration required to reproduce the issue
- Step-by-step instructions to reproduce the issue
- Proof-of-concept or exploit code (if possible)
- Impact of the issue, including how an attacker might exploit the issue

This information will help us triage your report more quickly.

## Policy

See [GitHub's Safe Harbor Policy](https://docs.github.com/en/github/site-policy/github-bug-bounty-program-legal-safe-harbor#1-safe-harbor-terms)

## Software Bill of Materials (SBOM)

We generate Software Bill of Materials (SBOM) for this project to provide complete visibility into the dependency tree, enabling compliance reporting, vulnerability tracking, and supply chain risk assessment.

### SBOM Generation

SBOMs are automatically generated on every release and attached to GitHub releases as downloadable assets.

Both SPDX and CycloneDX formats are generated to ensure compatibility with different compliance and security tools.

### Local SBOM Generation

To generate an SBOM locally, first install [syft](https://github.com/anchore/syft):

```bash
# Install syft
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin

# Generate SBOM
make sbom
```

This produces two files:
- `sbom.spdx.json` - SBOM in SPDX JSON format
- `sbom.cdx.json` - SBOM in CycloneDX JSON format

### SBOM Contents

The generated SBOMs include:
- All direct and transitive Go dependencies
- Package versions and licenses
- Package hashes for integrity verification
- Dependency relationships
