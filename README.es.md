# Lisa's Notes

Un plugin de Claude Code que registra automáticamente cada interacción completada con Claude como un commit en el sistema de control de versiones [Jujutsu](https://github.com/martinvonz/jj).

## ¿Por qué "Lisa's Notes"?

Al igual que Lisa Simpson documenta meticulosamente todo en su diario, este plugin mantiene un registro detallado de cada cambio que Claude realiza en tu base de código. Cada vez que terminas de trabajar con Claude, el plugin crea automáticamente un commit en Jujutsu usando la última respuesta de Claude como mensaje del commit, asegurando que nada se pierda ni se olvide.

## Cómo funciona

El plugin se engancha al evento "Stop" de Claude. Cuando finalizas una sesión de Claude:

1. El plugin lee la transcripción de la conversación de Claude (un archivo JSON Lines)
2. Analiza cada línea buscando mensajes del asistente
3. Extrae el **contenido de texto completo** del último mensaje del asistente (combinando todos los bloques de texto)
4. Crea un commit en Jujutsu usando ese mensaje completo como mensaje del commit
5. Todas las operaciones se registran en `/tmp/lisas-notes-debug.log` para resolución de problemas

Esto significa que cada interacción con Claude resulta en un commit discreto y bien documentado en tu historial de versiones.

## Requisitos

- [Jujutsu (jj)](https://github.com/martinvonz/jj) instalado y disponible en el PATH
- [jq](https://stedolan.github.io/jq/) para el procesamiento de JSON
- Tu proyecto debe ser un repositorio Jujutsu

## Instalación

1. Clona este repositorio:
   ```bash
   git clone <repository-url> lisas-notes
   ```

2. Registra el marketplace en Claude usando la ruta a `.claude-plugin/marketplace.json`

3. Activa el plugin lisas-notes desde el marketplace

## Uso

Una vez activado, el plugin funciona de forma automática:

1. Trabaja con Claude en un repositorio gestionado por Jujutsu
2. Cuando detengas la sesión de Claude (Ctrl+C o `/exit`), el plugin se activará
3. Tus cambios se confirmarán con la última respuesta de Claude como mensaje

No se necesita intervención manual: simplemente trabaja con Claude y deja que Lisa lleve el registro.

## Estructura del proyecto

```
lisas-notes/
├── .claude-plugin/
│   └── marketplace.json      # Configuración del marketplace
├── plugins/
│   └── lisas-notes/
│       ├── .claude-plugin/
│       │   └── plugin.json   # Metadatos del plugin (v1.0.5)
│       ├── hooks/
│       │   └── hooks.json    # Definición del hook de parada
│       └── scripts/
│           └── auto-commit.sh # El script de automatización de commits
└── README.md
```

## Depuración

Si los commits no se están creando como se espera, revisa el registro de depuración:

```bash
tail -f /tmp/lisas-notes-debug.log
```

Este registro muestra:
- Cuándo el hook comienza y termina
- La ruta de la transcripción que se está leyendo
- El mensaje del commit extraído
- Cualquier error del comando `jj commit`

## Autor

Ian Bull
