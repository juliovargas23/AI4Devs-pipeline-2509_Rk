## 📦 GitHub Actions CI/CD Prompts (3)
- Prompt 1: “Eres un DevOps engineer; crea un pipeline usando GitHub Actions para desplegar este proyecto React en una instancia EC2. Debes crear el archivo YAML que ejecute el pipeline cuando se genere un Pull Request. Primero, como prompt engineer experto, genera un prompt muy completo para crear este pipeline; realiza las preguntas necesarias con 3 opciones y tus recomendaciones.”  
  - Resultado: Se generó un prompt detallado para solicitar un workflow de PR con tests/build y despliegue a EC2, incluyendo preguntas de configuración y recomendaciones.
- Prompt 2: “Ajusta: se está usando una instancia EC2 con Amazon Linux (no Ubuntu).”  
  - Resultado: Se adaptó el prompt anterior para considerar Amazon Linux en el despliegue y requisitos del workflow.
- Prompt 3: “Mejora el prompt para incluir el full stack (frontend, backend con DB).”  
  - Resultado: Se amplió el prompt para cubrir build/test/despliegue de frontend y backend, Prisma/DB, PM2 y despliegue en Amazon Linux vía GitHub Actions.
