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

## Consultar Reportes

Una vez finalizada la ejecución de las pruebas, los reportes en formato HTML generados por JMeter se encontrarán en la subcarpeta correspondiente dentro de `target/jmeter/reports/`.

Para abrir un explorador (en Windows) directamente en la carpeta donde se encuentran los reportes recién generados, simplemente ejecuta:

```bash
mvn exec:exec
```

También puedes abrirlos manualmente navegando a, por ejemplo:

```text
target/jmeter/reports/CSVSample/index.html
```
*(Nota: El nombre específico de la carpeta del reporte, como `CSVSample`, dependerá del nombre del plan de pruebas `.jmx` que se haya ejecutado en la carpeta `src/test/jmeter`).*

## Ejecución con Docker Compose

Si prefieres no instalar Maven o Java en tu máquina, puedes ejecutar este proyecto utilizando Docker. El archivo `docker-compose.yml` preconfigurado descargará una imagen de Maven y ejecutará las pruebas de forma contenerizada y aislada.

Para lanzar las pruebas con Docker, simplemente ejecuta en la raíz del proyecto:

```bash
docker-compose up
```

Este comando:
1. Levantará un contenedor con Maven y Java 11.
2. Montará el código de este repositorio dentro del contenedor.
3. Descargará las dependencias necesarias en un volumen local (para que en futuras ejecuciones sea más rápido).
4. Ejecutará automáticamente el ciclo `mvn clean verify`.

Al finalizar, el contenedor se detendrá y podrás consultar los reportes en tu carpeta `target/` local de la misma manera que si lo hubieras ejecutado sin Docker.

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

Una vez configurada la variable de entorno, simplemente ejecuta el comando de verificación estándar:

```bash
mvn clean verify sonar:sonar
```

Esto realizará la ejecución de las pruebas de JMeter y enviará el reporte de análisis estático a [SonarCloud](https://sonarcloud.io) para el proyecto `mvn-jmeter` en la organización `arcralch`.
