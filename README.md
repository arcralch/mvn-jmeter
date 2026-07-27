# Proyecto Maven - Integración JMeter

Este repositorio contiene un proyecto configurado con Maven para la ejecución automatizada de pruebas de rendimiento utilizando **Apache JMeter** a través del plugin `jmeter-maven-plugin`.

## Requisitos Previos

- **Java JDK 11** o superior (si se ejecuta localmente).
- **Maven 3.6+** (si se ejecuta localmente).
- **Docker y Docker Compose** (opcional, para ejecución contenerizada).

## Ejecución Local

Para ejecutar los planes de prueba de JMeter localmente, abre una terminal en la raíz del proyecto y ejecuta:

```bash
mvn clean verify
```

### Generación de Reportes

El plugin `jmeter-maven-plugin` está configurado con `<generateReports>true</generateReports>`, lo que genera automáticamente un reporte HTML completo después de cada ejecución de pruebas.

Una vez finalizada la ejecución, los reportes en formato HTML se encontrarán en:

```
target/jmeter/reports/CSVSample/index.html
```

El reporte incluye:
- Resumen general de las pruebas
- Gráficos de tiempo de respuesta
- Gráficos de rendimiento (throughput)
- Gráficos de tiempo sobre el tiempo (over time)
- Métricas personalizadas

Para abrir directamente la carpeta de reportes (en Windows), ejecuta:

```bash
mvn exec:exec
```

## Ejecución con Docker Compose

Si prefieres no instalar Maven o Java en tu máquina, puedes ejecutar este proyecto utilizando Docker. El archivo `docker-compose.yml` descargará una imagen de Maven y ejecutará las pruebas de forma contenerizada y aislada.

Para lanzar las pruebas con Docker, ejecuta en la raíz del proyecto:

```bash
docker-compose up
```

Este comando:
1. Levanta un contenedor con Maven y Java 11.
2. Monta el código del repositorio dentro del contenedor (`/app`).
3. Monta un volumen para el caché de Maven (`~/.m2`) para acelerar ejecuciones futuras.
4. Monta un volumen para los reportes (`target/jmeter/reports`) para persistirlos localmente.
5. Ejecuta automáticamente `mvn clean verify`.
6. Al finalizar, muestra un mensaje indicando la ubicación del reporte.

Al finalizar, los reportes se encontrarán en tu carpeta local:

```
target/jmeter/reports/CSVSample/index.html
```

## Ejecución con GitHub Actions

El proyecto incluye un workflow de GitHub Actions (`/.github/workflows/jmeter-tests.yml`) que ejecuta las pruebas automáticamente en cada push o pull request a las ramas `main` y `master`.

### Características del Workflow

- **Run on**: Ubuntu latest con JDK 11
- **Comandos ejecutados**: `mvn clean verify`
- **Reporte HTML**: Generado automáticamente por el plugin de JMeter
- **Resultados JTL**: Guardados en `target/jmeter/results/`

### Artefactos Generados

Después de cada ejecución del workflow, se generan dos artefactos descargables:

| Artefacto | Contenido | Retención |
|-----------|-----------|-----------|
| `jmeter-report` | Reporte HTML completo (todo el contenido de `target/jmeter/reports/`) | 30 días |
| `jmeter-results` | Archivos JTL con resultados brutos (todo el contenido de `target/jmeter/results/`) | 30 días |

### Cómo descargar el reporte HTML

1. Ve a la pestaña **Actions** en tu repositorio de GitHub.
2. Haz clic en la ejecución del workflow que deseas.
3. Desplázate hacia abajo hasta la sección **Artifacts**.
4. Haz clic en el artefacto **jmeter-report**.
5. Se descargará un archivo `.zip` con todo el reporte HTML.
6. Descomprime el archivo y abre `CSVSample/index.html` en tu navegador.

### Disparadores del Workflow

- Push a las ramas: `main`, `master`
- Pull requests a las ramas: `main`, `master`
- Ejecución manual (workflow_dispatch)

## Análisis de Código con SonarCloud

Se ha integrado el análisis de código con **SonarCloud** utilizando el plugin `sonar-maven-plugin`. El análisis se ejecuta automáticamente durante la fase `verify` de Maven.

### Configuración del Token

Para que el análisis se comunique correctamente con SonarCloud, es necesario configurar una variable de entorno con tu token de acceso (`SONAR_TOKEN`):

**En Linux/macOS:**
```bash
export SONAR_TOKEN="tu_token_generado_en_sonarcloud"
```

**En Windows (PowerShell):**
```powershell
$env:SONAR_TOKEN="tu_token_generado_en_sonarcloud"
```

**En Windows (CMD):**
```cmd
set SONAR_TOKEN=tu_token_generado_en_sonarcloud
```

### Ejecución del Análisis

Una vez configurada la variable de entorno, simplemente ejecuta:

```bash
mvn clean verify
```

El workflow de GitHub Actions también ejecuta el análisis automáticamente si la variable `SONAR_TOKEN` está configurada como un secret en la configuración del repositorio.

## Estructura del Proyecto

```
mvn-jmeter/
├── src/
│   └── test/
│       └── jmeter/
│           └── CSVSample.jmx          # Plan de pruebas de ejemplo
├── target/
│   ├── jmeter/
│   │   ├── reports/                   # Reportes HTML generados
│   │   │   └── CSVSample/
│   │   │       ├── index.html         # Página principal del reporte
│   │   │       └── content/pages/     # Gráficos y métricas
│   │   └── results/                   # Resultados JTL brutos
├── pom.xml                            # Configuración Maven
├── docker-compose.yml                 # Configuración Docker
└── .github/workflows/jmeter-tests.yml # Workflow GitHub Actions
```

## Solución de Problemas

### Error de Java version (class file version)

Si encuentras errores relacionados con la versión de Java, asegúrate de que el plugin de SonarQube sea compatible con tu versión de Java. Actualmente se usa:

- Plugin SonarQube: `3.9.1.2184` (compatible con Java 11)

### Reportes no se generan

Verifica que en el `pom.xml` el plugin de JMeter tenga:

```xml
<configuration>
    <generateReports>true</generateReports>
</configuration>
```

### GitHub Actions falla en SonarQube

Si el workflow falla en la fase de SonarQube, asegúrate de que la variable `SONAR_TOKEN` esté configurada como un secret en la configuración del repositorio de GitHub.
