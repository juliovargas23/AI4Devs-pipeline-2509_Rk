## 📦 GitHub Actions CI/CD Prompts (3)
- Prompt 1: “Eres un DevOps engineer; crea un pipeline usando GitHub Actions para desplegar este proyecto React en una instancia EC2. Debes crear el archivo YAML que ejecute el pipeline cuando se genere un Pull Request. Primero, como prompt engineer experto, genera un prompt muy completo para crear este pipeline; realiza las preguntas necesarias con 3 opciones y tus recomendaciones.”  
  - Resultado: Se generó un prompt detallado para solicitar un workflow de PR con tests/build y despliegue a EC2, incluyendo preguntas de configuración y recomendaciones.
- Prompt 2: “Ajusta: se está usando una instancia EC2 con Amazon Linux (no Ubuntu).”  
  - Resultado: Se adaptó el prompt anterior para considerar Amazon Linux en el despliegue y requisitos del workflow.
- Prompt 3: “Mejora el prompt para incluir el full stack (frontend, backend con DB).”  
  - Resultado: Se amplió el prompt para cubrir build/test/despliegue de frontend y backend, Prisma/DB, PM2 y despliegue en Amazon Linux vía GitHub Actions.

## Master prompt utilizado:
Eres un DevOps senior. Crea un workflow de GitHub Actions (archivo .github/workflows/deploy.yml) para un proyecto full-stack (React frontend + Express/Prisma backend). El workflow debe ejecutarse en cada Pull Request y hacer:

- Frontend:
  - Instalar deps con npm en frontend/, ejecutar npm run test y npm run build.
  - Empaquetar build de frontend (frontend/build).

- Backend:
  - Instalar deps con npm en backend/, ejecutar npm run test y npm run build.
  - Empaquetar backend/dist y los archivos necesarios (package.json, package-lock.json, prisma schema si aplica).
  - Preparar comando remoto para `npx prisma migrate deploy` (opcional, controlado por flag/env).
  - Cargar env vars del backend desde secrets (DATABASE_URL, JWT, etc.).

- Despliegue a EC2 (Amazon Linux):
  - Conectarse por scp + ssh usando clave desde secrets.
  - Copiar artefactos a /var/www/app/current (sobrescribir), con subcarpetas frontend/ y backend/.
  - Asegurar creación de directorios y permisos (mkdir -p, chown/chmod si aplica).
  - Reiniciar backend con pm2 reload <app>. Si no existe, opcionalmente pm2 start con script (por ejemplo, `pm2 start dist/index.js --name <app>`).
  - (Opcional) Ejecutar prisma migrate deploy antes de pm2 reload.

- Seguridad y caché:
  - Usar secrets de GitHub (sin exponerlos).
  - Cache npm para acelerar.
  - ssh/scp con StrictHostKeyChecking=no sólo si no hay known_hosts.

- Control de flujo:
  - runs-on: ubuntu-latest.
  - Disparar sólo en pull_request hacia main y develop (ajustable).
  - Concurrencia: concurrency: group: deploy-${{ github.head_ref || github.ref }} cancel-in-progress: true
  - Publicar comentarios de éxito/fallo en el PR (github-script). Si falla, marcar job failed.

Config fija (respuestas dadas):
1) Gestor de paquetes: npm
2) Build/lint/tests: npm run test && npm run build
3) Despliegue: scp + ssh
4) Directorio remoto: /var/www/app/current (sobrescribir)
5) Reinicio: pm2 reload <app>
6) SO destino: Amazon Linux (usa yum si necesitas instalar node/npm/pm2 en remoto)

Secrets esperados (usa estos nombres en el YAML):
- AWS_ACCESS_ID
- AWS_ACCESS_KEY
- EC2_INSTANCE (IP/DNS)
- EC2_SSH_PRIVATE_KEY (clave privada completa)
- EC2_USER (p. ej. ec2-user)
- Opcionales/recomendados para backend/DB: DATABASE_URL, BACKEND_ENV (base64 de .env backend), FRONTEND_ENV (si se requiere), SSH_PORT (si no es 22).

Requisitos extra:
- Copia sólo artefactos construidos (no compiles en EC2).
- Incluye pasos para crear /var/www/app/current/frontend y /var/www/app/current/backend y limpiar el contenido previo de manera segura.
- Si usas pm2 y no existe el proceso, arráncalo; en siguientes despliegues, reload.
- Si habilitas prisma migrate deploy, hazlo antes del reload y falla si hay error.
- Devuelve el YAML completo y listo para pegar, con comentarios breves.
