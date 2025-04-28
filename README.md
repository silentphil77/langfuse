# Langfuse + Postgres Docker Setup

This is a production-ready, conflict-free Docker Compose deployment of [Langfuse](https://github.com/langfuse/langfuse) with Postgres.

## What is Langfuse?

Langfuse is an open-source LLM engineering platform that helps teams collaboratively develop, monitor, evaluate, and debug AI applications. It provides:

- **LLM Application Observability**: Track LLM calls and application logic like retrieval, embedding, or agent actions
- **Prompt Management**: Centrally manage, version control, and iterate on prompts
- **Evaluations**: Support for LLM-as-a-judge, user feedback, and custom evaluation pipelines
- **Datasets**: Create test sets and benchmarks for evaluating your LLM applications
- **LLM Playground**: Quickly test and iterate on prompts and model configurations

## 🔧 Setup

1. Clone the repo:
   ```bash
   git clone https://github.com/your-org/langfuse-docker.git
   cd langfuse-docker
   ```

2. Create a `.env` file:
   ```bash
   cp env.example .env
   ```

3. Configure your `.env` file:
   - Update `SERVICE_FQDN_LANGFUSE_3010` with your domain
   - Set secure passwords for `POSTGRES_PASSWORD`, `SERVICE_PASSWORD_SALT`, `SERVICE_PASSWORD_LANGFUSE`
   - Generate a secure random string for `NEXTAUTH_SECRET` (at least 64 characters, base64 encoded)
     ```bash
     # Example command to generate a secure random string
     openssl rand -base64 64
     ```

4. Start the services:
   ```bash
   docker compose up -d
   ```

5. Access the Langfuse UI:
   - Navigate to your configured domain or http://localhost:3010 if running locally
   - Login with the default admin credentials:
     - Email: admin@example.com
     - Password: (value of SERVICE_PASSWORD_LANGFUSE)

## 📁 Directory Structure
```
langfuse-docker/
├── docker-compose.yml      (Main configuration for Docker services)
├── env.example             (Example .env file for setup)
├── .env                    (Created by you - contains your configuration)
└── README.md               (This file)
```

## ⚙️ Environment Variables

The `.env` file should have these variables:

```
# Langfuse public URL (used in NEXTAUTH redirect logic)
SERVICE_FQDN_LANGFUSE_3010=https://yourdomain.com

# Postgres
POSTGRES_DB=langfuse
POSTGRES_USER=langfuse_user
POSTGRES_PASSWORD=yourpassword

# Langfuse Auth
SERVICE_PASSWORD_SALT=randomsalt
SERVICE_PASSWORD_LANGFUSE=adminpassword
NEXTAUTH_SECRET=<secure-random-string>
```

## 🔌 Integrations

Langfuse provides multiple ways to integrate with your LLM applications:

- **SDKs**: Python, JS/TS
- **OpenAI Integration**: Drop-in replacement for OpenAI SDK
- **Framework Support**: LangChain, LlamaIndex, Haystack, LiteLLM, and more
- **Public API**: REST API with OpenAPI spec

## 🛠️ Maintenance

- **Backup**: Postgres data is stored in a named volume `pg-data`. Back up this volume regularly.
  ```bash
  # Example backup command
  docker run --rm -v pg-data:/data -v $(pwd)/backups:/backups alpine tar -czf /backups/pg-data-$(date +%Y%m%d).tar.gz /data
  ```

- **Updates**: To update Langfuse, modify the image tag in docker-compose.yml and restart:
  ```bash
  # Pull the latest image and restart
  docker compose pull
  docker compose up -d
  ```

- **Logs**: View logs for troubleshooting:
  ```bash
  docker compose logs -f langfuse
  ```

## 🔒 Security Notes

- Ensure your server has proper firewall rules
- Use a reverse proxy with SSL termination (like Nginx or Traefik)
- Set strong passwords in your `.env` file
- Consider restricting signup by setting `AUTH_DISABLE_SIGNUP=true` in docker-compose.yml
- Regularly update your deployment for security patches

## 🔍 Monitoring

- **Health Check**: The Langfuse container includes a health check at `/api/public/health`
- **Metrics**: Monitor container metrics using Docker's built-in tools or a monitoring solution

## ❓ Troubleshooting

- **Database Connection Issues**: Verify Postgres container is running and credentials are correct
- **Authentication Problems**: Check NEXTAUTH_SECRET and SERVICE_FQDN_LANGFUSE_3010 values
- **Container Startup Failures**: View logs with `docker compose logs`

## 📚 Additional Resources

- [Langfuse Documentation](https://langfuse.com/docs)
- [Self-hosting Guide](https://langfuse.com/self-hosting)
- [Langfuse GitHub Repository](https://github.com/langfuse/langfuse) 