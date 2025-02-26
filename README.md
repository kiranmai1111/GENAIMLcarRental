# Infrastructure & Application Setup for Car Rental Service

Connected to the instance via Session Manager
Installed essential dependencies:

sudo apt update && sudo apt install -y openjdk-17-jdk unzip

Did the application set up:
Cloned the AIOPS Hackathon_Lab_Application into home Directory through
git clone https://github.com/Milinda96/AIOPs_Hackathon_Lab_Application.git

Ran the microservices manually for initial validation:

nohup java -Xms256m -Xmx512m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./java_pid.hprof -jar /home/ubuntu/discovery-service/discovery-service-0.0.1-SNAPSHOT.jar > discovery-service.log 2>&1 &


nohup java -Xms256m -Xmx512m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./java_pid.hprof -jar /home/ubuntu/api-gateway/api-gateway-0.0.1-SNAPSHOT.jar > api-gateway.log 2>&1 &


nohup java -Xms256m -Xmx512m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./java_pid.hprof -jar /home/ubuntu/location-service/location-service-0.0.1-SNAPSHOT.jar > location.log 2>&1 &


nohup java -Xms256m -Xmx512m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./java_pid.hprof -jar /home/ubuntu/car-service/car-service-0.0.1-SNAPSHOT.jar > car-service.log 2>&1 &


nohup java -Xms256m -Xmx512m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./java_pid.hprof -jar /home/ubuntu/reservation-service/reservation-service-0.0.1-SNAPSHOT.jar > reservation.log 2>&1 &


 Logging & Monitoring (CloudWatch):

# Installed and configured the CloudWatch Agent for metrics and logs:
 
sudo apt install -y amazon-cloudwatch-agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
  -s

Started the agent and verified logs in AWS CloudWatch.
sudo systemctl start amazon-cloudwatch-agent



 Observability Setup (OpenTelemetry & Metrics):

Installed and Ran OpenTelemetry Collector
The OpenTelemetry Collector is responsible for collecting traces, metrics, and logs from your application and forwarding them to AWS CloudWatch.

# Downloaded OpenTelemetry Collector
wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/latest/download/otelcol-linux-amd64 -O otelcol

#Made it executable
chmod +x otelcol

#Moved it to a standard location
sudo mv otelcol /usr/local/bin/

# Verified installation
otelcol --version

Started the open telemtry collector:
sudo systemctl restart otel-collector
checked it status:
sudo systemctl status otel-collector

Registered Application Metrics in Java
You added metrics instrumentation to your Java-based microservices using Micrometer and OpenTelemetry SDK.

Added Dependencies (Spring Boot Example)
You included Micrometer and OpenTelemetry dependencies in  pom.xml

created a  OpenTelemetry Collector Configuration at otel-config.yaml

updated application.properties file in every microservice to enable actuator end points at application.properties


# Installed and Configured OpenTelemetry Java Agent 

Downloaded the OpenTelemetry Java Agent:

curl -L https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar -o opentelemetry-javaagent.jar

Started microservices with OpenTelemetry Agent:

java -javaagent:/path/to/opentelemetry-javaagent.jar -jar your-microservice.jar

Set OpenTelemetry environment variables:
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
export OTEL_SERVICE_NAME=location-service
export OTEL_METRICS_EXPORTER=otlp
export OTEL_TRACES_EXPORTER=otlp
export OTEL_LOGS_EXPORTER=otlp

Configured OpenTelemetry Collector

Set up the OpenTelemetry Collector to collect and forward traces, logs, and metrics.
Configured exporters to send telemetry data to AWS CloudWatch.
Exposed Custom Metrics in Java

Integrated Micrometer for metric instrumentation.
Registered a custom metric (api.requests.count) to track API request counts in the application.
Integrated Logs and Metrics with CloudWatch

Used CloudWatch Log Insights for structured log queries and analysis.
Created Dashboards & Alarms

Designed CloudWatch Dashboards to visualize key performance metrics.
Set up CloudWatch Alarms to trigger notifications based on metric thresholds, ensuring proactive monitoring
