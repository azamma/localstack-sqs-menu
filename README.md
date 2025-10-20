# LocalStack Interactive Menu - AWS SQS Testing Tool

This PowerShell script provides an interactive menu for testing AWS SQS (Simple Queue Service) locally using LocalStack. It automates the setup, configuration, and testing of SQS queues without requiring actual AWS credentials or incurring cloud costs.

## What Does It Do?

✅ **Automatic LocalStack setup** with Docker
✅ **Interactive menu** for SQS operations
✅ **Create Standard and FIFO queues** easily
✅ **Send and verify messages** with JSON validation
✅ **List all queues** in the current session
✅ **Automatic cleanup** on exit
✅ **No AWS costs** - fully local testing environment

## Features

- 🚀 Automated Docker container management
- 📋 Menu-driven interface for SQS operations
- 🔢 Bulk queue creation with sequence feature
- 🔍 Connection health checks with retry logic
- ✅ JSON message validation before sending
- 🧪 Automatic message verification after sending
- 🧹 Clean shutdown and resource cleanup
- 📊 Support for both Standard and FIFO queues

## Prerequisites

Before running this script, ensure you have the following installed:

### Required Software

1. **Docker Desktop**
   - Download from: https://www.docker.com/products/docker-desktop
   - Ensure Docker is running before executing the script

2. **PowerShell 5.1+**
   - Included with Windows 10/11
   - Run PowerShell as Administrator for best results

3. **Python & pip** (for awslocal CLI)
   - Download from: https://www.python.org/downloads/
   - pip is included with Python 3.4+

4. **AWS CLI Local** (automatically installed by the script)
   - The script will install `awscli-local` via pip if not found

## Installation

### Quick Start

1. **Clone or download this repository:**
   ```powershell
   git clone https://github.com/yourusername/localstack-menu.git
   cd localstack-menu
   ```

2. **Run the script:**
   ```powershell
   .\LocalStackMenu.ps1
   ```

That's it! The script will handle all setup automatically.

### What Happens on First Run

The script will automatically:
1. ✅ Clean up any existing LocalStack containers
2. ✅ Verify Docker is running
3. ✅ Check port 4566 availability
4. ✅ Pull the latest LocalStack image
5. ✅ Start LocalStack with SQS service enabled
6. ✅ Verify connectivity with health checks
7. ✅ Install `awslocal` CLI if needed
8. ✅ Configure dummy AWS credentials for local use

## Usage

### Starting the Script

Open PowerShell and run:

```powershell
.\LocalStackMenu.ps1
```

### Interactive Menu

Once started, you'll see the following menu:

```
========= MENU LOCALSTACK ==========
============= by Zamma =============
1. Crear una cola
2. Crear secuencia de colas
3. Enviar un mensaje a una cola
4. Listar colas existentes
5. Salir
====================================
```

### Menu Options

#### 1. Create a Queue (Crear una cola)

Creates a new SQS queue (Standard or FIFO):

1. Enter a queue name
2. Choose queue type:
   - **Standard Queue**: High throughput, best-effort ordering
   - **FIFO Queue**: Guaranteed ordering, exactly-once processing

**Example:**
```
Ingresa el nombre de la cola: my-test-queue
Es una cola FIFO? (s/n) [n]: n

Cola Standard creada exitosamente:
  Nombre: my-test-queue
  URL: http://localhost:4566/000000000000/my-test-queue
```

#### 2. Create Queue Sequence (Crear secuencia de colas)

Creates multiple SQS queues at once with automatic naming:

1. Enter the number of queues to create (1-100)
2. Enter the base name for the queues
3. Choose queue type (Standard or FIFO)

The queues will be named: `basename`, `basename1`, `basename2`, ..., `basenameN`

**Example:**
```
Ingresa la cantidad de colas a crear: 5
Ingresa el nombre base de las colas: test-queue
Son colas FIFO? (s/n) [n]: n

Creando 5 colas de tipo Standard...

[1/5] Cola creada: test-queue
[2/5] Cola creada: test-queue1
[3/5] Cola creada: test-queue2
[4/5] Cola creada: test-queue3
[5/5] Cola creada: test-queue4

=== RESUMEN ===
Colas creadas exitosamente: 5
Total en el sistema: 5
```

**Use Cases:**
- 🔄 **Load testing:** Create multiple queues quickly for testing
- 🧪 **Batch testing:** Test message distribution across queues
- 📊 **Performance testing:** Simulate multiple queue scenarios
- 🎯 **Development:** Quickly set up test environments

#### 3. Send a Message (Enviar un mensaje a una cola)

Sends a JSON message to a selected queue:

1. Select a queue from the list
2. Enter a valid JSON message
3. For FIFO queues, optionally specify a Message Group ID
4. The script will automatically verify the message was received

**Example:**
```
Colas disponibles:
  1. my-test-queue (Standard)
Selecciona el numero de cola (1-1): 1
Ingresa el mensaje JSON:
{"orderId": "12345", "customer": "John Doe"}

Mensaje JSON valido
Mensaje enviado exitosamente:
  Cola: my-test-queue
  Mensaje: {"orderId": "12345", "customer": "John Doe"}

Verificando mensaje en la cola...
Mensaje recibido en la cola:
  {"orderId": "12345", "customer": "John Doe"}
El mensaje recibido coincide con el enviado
```

#### 4. List Queues (Listar colas existentes)

Displays:
- Queues created in the current session
- All queues in LocalStack (including those from previous sessions)

**Example:**
```
=== COLAS EXISTENTES ===
Colas creadas en esta sesion:
  1. my-test-queue - Tipo: Standard
     URL: http://localhost:4566/000000000000/my-test-queue

Todas las colas en LocalStack:
  - my-test-queue
    http://localhost:4566/000000000000/my-test-queue
```

#### 5. Exit (Salir)

Performs cleanup:
- Deletes all queues created in the session
- Stops the LocalStack container
- Removes Docker resources

## Configuration

### LocalStack Settings

The script uses the following LocalStack configuration:

- **Port:** 4566 (LocalStack edge port)
- **Services:** SQS only
- **Debug Mode:** Enabled
- **Legacy SQS Behavior:** Enabled for compatibility

### AWS Configuration

Dummy credentials are automatically configured:
- **Access Key ID:** test
- **Secret Access Key:** test
- **Region:** us-east-1
- **Profile:** localstack

## Advanced Usage

### Manual Queue Operations

You can also use `awslocal` CLI directly:

```powershell
# List queues
awslocal sqs list-queues --region us-east-1 --profile localstack

# Send a message
awslocal sqs send-message --queue-url http://localhost:4566/000000000000/my-queue --message-body "Hello World" --region us-east-1 --profile localstack

# Receive messages
awslocal sqs receive-message --queue-url http://localhost:4566/000000000000/my-queue --region us-east-1 --profile localstack

# Delete a queue
awslocal sqs delete-queue --queue-url http://localhost:4566/000000000000/my-queue --region us-east-1 --profile localstack
```

### Testing FIFO Queues

FIFO queues require:
- Queue name ending with `.fifo`
- Message Group ID (groups messages for ordered processing)
- Message Deduplication ID (prevents duplicate messages)

The script handles these requirements automatically.

## Troubleshooting

### Docker Not Running

**Error:** `Error con Docker: Docker daemon is not running`

**Solution:**
- Start Docker Desktop
- Wait for Docker to fully initialize
- Retry the script

### Port 4566 Already in Use

**Error:** `Puerto 4566 en uso`

**Solution:**
```powershell
# Find the process using port 4566
netstat -ano | findstr :4566

# Kill the process (replace PID with actual process ID)
taskkill /PID <PID> /F

# Or stop existing LocalStack containers
docker stop localstack-test
docker rm localstack-test
```

### LocalStack Not Responding

**Error:** `LocalStack no respondio despues de 20 intentos`

**Solution:**
1. Check Docker logs:
   ```powershell
   docker logs localstack-test
   ```
2. Ensure Docker has enough resources (at least 2GB RAM)
3. Restart Docker Desktop
4. Pull the latest LocalStack image:
   ```powershell
   docker pull localstack/localstack:latest
   ```

### awslocal Not Found

**Error:** `awslocal no encontrado`

**Solution:**
The script will attempt to install it automatically. If it fails:
```powershell
# Install manually
pip install awscli-local

# Or upgrade
pip install --upgrade awscli-local
```

### JSON Validation Error

**Error:** `El mensaje no es un JSON valido`

**Solution:**
Ensure your message is valid JSON:

✅ **Valid:**
```json
{"key": "value"}
{"orderId": 123, "status": "pending"}
```

❌ **Invalid:**
```
Hello World
{key: value}
{'key': 'value'}
```

### Permission Errors

**Error:** `Access Denied` or `Execution Policy` errors

**Solution:**
Run PowerShell as Administrator and allow script execution:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## Technical Details

### Script Workflow

1. **Initialization Phase:**
   - Cleans up existing containers
   - Verifies Docker availability
   - Checks port 4566

2. **Setup Phase:**
   - Pulls LocalStack image
   - Starts container with SQS service
   - Waits for health check (max 20 attempts)
   - Installs/verifies awslocal CLI
   - Configures dummy AWS credentials

3. **Interactive Phase:**
   - Displays menu
   - Processes user commands
   - Tracks created queues in memory

4. **Cleanup Phase:**
   - Deletes created queues
   - Stops and removes container
   - Cleans up Docker resources

### Queue Types

| Feature | Standard Queue | FIFO Queue |
|---------|---------------|------------|
| Throughput | Unlimited | Up to 3,000 msg/sec |
| Ordering | Best-effort | Guaranteed FIFO |
| Exactly-Once | No | Yes |
| Message Group | Not supported | Required |
| Deduplication | Not supported | Automatic |
| Name Suffix | Any | Must end with `.fifo` |

### Environment Variables

The script uses these environment variables internally:
- `$ContainerName`: Name of the Docker container (`localstack-test`)
- `$Global:CreatedQueues`: Array tracking queues created in the session

## Use Cases

This tool is perfect for:

- 🧪 **Local Development:** Test SQS integration without AWS costs
- 🎓 **Learning:** Understand SQS concepts hands-on
- 🔬 **Testing:** Validate message handling logic
- 🚀 **CI/CD:** Automated integration tests
- 📚 **Demos:** Show SQS functionality offline

## Best Practices

1. **Always use valid JSON** for message bodies
2. **Use FIFO queues** when message ordering matters
3. **Test with realistic payloads** to simulate production
4. **Monitor Docker resources** for long testing sessions
5. **Clean up regularly** to avoid resource accumulation

## Limitations

- LocalStack is a **simulation** - some AWS SQS features may behave differently
- The free version of LocalStack has limited features compared to AWS
- Designed for **development and testing only** - not for production
- Port 4566 must be available on your machine

## Contributing

Contributions are welcome! Please feel free to:
- Report bugs or issues
- Suggest new features
- Submit pull requests
- Improve documentation

## License

This project is provided as-is for educational and development purposes.

## Credits

**Created by:** Zamma
**LocalStack:** https://localstack.cloud/
**AWS SQS Documentation:** https://docs.aws.amazon.com/sqs/

## Support

If you encounter issues:
1. Check the [Troubleshooting](#troubleshooting) section
2. Review Docker and LocalStack logs
3. Ensure all prerequisites are installed
4. Open an issue with detailed error messages

---

**Happy Testing!** 🚀
