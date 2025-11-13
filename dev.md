Prerequisites
===========
* **[OpenJDK 21](https://openjdk.java.net/)** - PDFsam requires JDK 21 (NOT JDK 11)
* **[Maven](http://maven.apache.org/)** - Build tool for dependency management
* **[Gnu gettext](https://www.gnu.org/software/gettext/)** - Required for internationalization
  * Windows: http://mlocati.github.io/gettext-iconv-windows/
  * macOS: Install via Homebrew or use Docker (see below)
  * Linux: Usually pre-installed or available via package manager

Build Instructions
===========

## Initial Setup
1. [Clone the PDFsam repository](https://help.github.com/articles/cloning-a-repository/)
2. Checkout the tag/branch you want to build (e.g., `git checkout v5.4.1`)

## macOS
**Note:** Homebrew's OpenJDK 21 does not work. Use Liberica OpenJDK 21 instead.

1. Download and install [Liberica JDK 21](https://bell-sw.com/pages/downloads/#jdk-21-lts)
2. Set JAVA_HOME and build:
```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/liberica-jdk-21.jdk/Contents/Home

# Build with tests
mvn clean package -Drelease

# Build without tests (faster)
mvn clean package -Drelease -DskipTests
```

**Note:** You may see a signing certificate error - this can be safely ignored for local testing.

## Linux

### Native Build
If you have JDK 21 and gettext installed:
```bash
# Build with tests
mvn clean package -Drelease

# Build without tests (faster)
mvn clean package -Drelease -DskipTests
```

### Docker Build (Linux only)
Build without installing dependencies on your system using Docker:
```bash
docker run --rm -v "$PWD":/app -w /app mcr.microsoft.com/openjdk/jdk:21-ubuntu \
  bash -c "apt-get update && apt-get install -y maven gettext && mvn clean package -Drelease -DskipTests"
```

**Important Notes:**
- Use Microsoft's OpenJDK image (`mcr.microsoft.com/openjdk/jdk:21-ubuntu`) as it includes the full JDK with jmods directory required for `jlink`
- The Liberica `liberica-openjdk-debian:21` image lacks jmods and will fail during the jlink step
- **macOS users:** The Docker build creates Linux-specific binaries (bundled runtime + JavaFX native libraries) that cannot run on macOS. You must use the native macOS build (see macOS section above) to create runnable macOS applications

Run
===========
After building, find the assembled application in `pdfsam-basic/target/assembled/`:
```bash
pdfsam-basic/target/assembled/bin/pdfsam.sh
```