
# 📡 Observation and Instrumentation

## 🔍 What is Monitoring and Observability?

| Concept         | Description |
|----------------|-------------|
| **Monitoring** | Collecting and analyzing predefined metrics to detect and respond to system issues.<br>**Example**: Triggering an alert when CPU usage exceeds 80%. |
| **Observability** | The ability to understand the internal state of a system based on the system’s external outputs (**Telemetry Data**).<br>**Example**: Diagnosing unknown issues or unusual behaviors. |

---

## 📦 What is Instrumentation?

> **Instrumentation** is the practice of adding code or using tools to collect **telemetry data** from software, systems, or infrastructure (like applications, networks, or operating systems).

---

## 📊 Types of Telemetry Data

| Type     | Description |
|----------|-------------|
| **Logs** | Application logs, system logs, access logs |
| **Metrics** | CPU usage, memory usage, request counts, error rates |
| **Traces** | Distributed tracing (e.g., tracking a request through multiple microservices) |

---

## 🧪 Practical Setup: Java App Observability on Ubuntu

### 1️⃣ Install Java

```bash
sudo apt update -y
sudo apt install openjdk-21-jdk -y

# Verify
java -version
javac -version
```

### 2️⃣ Install Maven

```bash
sudo apt update -y
sudo apt install maven -y

# Verify
mvn -version
```

### 3️⃣ Install Docker

```bash
# Run official Docker install script
curl -fsSL https://get.docker.com | bash

# Set permissions
sudo chmod 777 /var/lib/docker.sock

# Verify
docker --version
```

### 4️⃣ Set Java and Maven Environment Paths

```bash
nano ~/.bashrc
```

Add the following lines at the end of the file:

```bash
# Java
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH

# Maven
export MAVEN_HOME=/usr/share/maven
export PATH=$MAVEN_HOME/bin:$PATH
```

Apply changes:

```bash
source ~/.bashrc

# Verify
echo $JAVA_HOME
echo $MAVEN_HOME
```

---

### 5️⃣ Clone GitHub Repository and Build the Application

```bash
git clone https://github.com/shubhamdhole97/opentelemetry-spring-boot-starter.git
cd opentelemetry-spring-boot-starter/exercises/automatic-instrumentation/initial/todobackend-springboot
```

Add the following dependency inside the `pom.xml` file:

```xml
<dependency>
    <groupId>io.opentelemetry.instrumentation</groupId>
    <artifactId>opentelemetry-instrumentation-annotations</artifactId>
    <version>2.4.0</version>
</dependency>
```

Then build the app:

```bash
mvn clean
mvn package
```

This creates the output JAR file under the `target` folder: `todobackend-0.0.1-SNAPSHOT.jar`

---

### 🟡 Run Jaeger for Tracing

```bash
docker run -d --name jaeger \
  -e COLLECTOR_OTLP_ENABLED=true \
  -p 16686:16686 \
  -p 14268:14268 \
  -p 1317:4317 \
  -p 4318:4318 \
  jaegertracing/all-in-one
```

Set the Envirniment variable for traces 
```bash
export OTEL_TRACES_EXPORTER=otlp
export OTEL_METRICS_EXPORTER=none
export OTEL_LOGS_EXPORTER=none
```
---

### 🟣 Add OpenTelemetry Agent (Automatic Instrumentation)

1. Download the OpenTelemetry agent:

```bash
wget https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v2.8.0/opentelemetry-javaagent.jar
```

2. Run the app with the agent:

```bash
java -javaagent:opentelemetry-javaagent.jar -jar todobackend-automatic-0.0.1-SNAPSHOT.jar
```

Go to the Webbrowser and visit http://localhost:16686

Now will do some POST and DELETE Operation to check for traces

```bash
curl -X POST localhost:8080/todos/NEW
```
```bash
curl -X DELETE localhost:8080/todos/SHUBHAMDHOLE
```

Go inside and check the traces inside Jaeger UI


