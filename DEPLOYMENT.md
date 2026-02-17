# 🚀 Guía de Deployment a Google Cloud Run

## Prerequisitos

1. **Cuenta de Google Cloud Platform (GCP)**
   - Crea una cuenta gratuita en https://cloud.google.com
   - $300 de créditos gratuitos para nuevos usuarios

2. **Instalar Google Cloud SDK**
   ```bash
   # macOS (con Homebrew)
   brew install google-cloud-sdk
   
   # O descarga desde: https://cloud.google.com/sdk/docs/install
   ```

3. **Instalar Docker**
   ```bash
   # macOS (con Homebrew)
   brew install docker
   
   # O descarga Docker Desktop: https://www.docker.com/products/docker-desktop
   ```

## Pasos para Deployment

### 1. Autenticarse en Google Cloud

```bash
# Iniciar sesión con tu cuenta de Google
gcloud auth login

# Configurar Docker para usar GCR
gcloud auth configure-docker
```

### 2. Crear un Proyecto en GCP (si no tienes uno)

```bash
# Crear proyecto
gcloud projects create tu-proyecto-id --name="Fundamentos Digitales"

# Habilitar billing (requerido para Cloud Run)
# Esto debes hacerlo desde la consola web: https://console.cloud.google.com/billing

# Habilitar APIs necesarias
gcloud services enable run.googleapis.com containerregistry.googleapis.com
```

### 3. Configurar el Script de Deployment

Edita `deploy.sh` y cambia la variable `PROJECT_ID`:

```bash
PROJECT_ID="tu-proyecto-id-real"  # Cambiar a tu ID de proyecto
```

### 4. Ejecutar el Deployment

```bash
cd /Users/felipe/.gemini/antigravity/playground/eternal-flare

# Método 1: Usar el script automatizado
./deploy.sh

# Método 2: Paso a paso manual (si prefieres control total)
# Ver sección "Deployment Manual" más abajo
```

### 5. Acceder a tu eBook

Después del deployment, Cloud Run te dará una URL como:
```
https://fundamentos-digitales-xxxxx-uc.a.run.app
```

¡Tu ebook estará disponible públicamente en esa URL! 🎉

---

## Deployment Manual (Alternativa al Script)

Si prefieres ejecutar los comandos uno por uno:

```bash
# 1. Configurar proyecto
export PROJECT_ID="tu-proyecto-id"
gcloud config set project ${PROJECT_ID}

# 2. Construir imagen
docker build -t gcr.io/${PROJECT_ID}/fundamentos-digitales .

# 3. Push a GCR
docker push gcr.io/${PROJECT_ID}/fundamentos-digitales

# 4. Deploy a Cloud Run
gcloud run deploy fundamentos-digitales \
    --image gcr.io/${PROJECT_ID}/fundamentos-digitales \
    --platform managed \
    --region us-central1 \
    --allow-unauthenticated \
    --port 8080 \
    --memory 256Mi \
    --max-instances 10
```

---

## Actualizar el eBook (Re-deployment)

Cuando hagas cambios al contenido:

```bash
# Simplemente ejecuta el script de nuevo
./deploy.sh

# Cloud Run automáticamente:
# 1. Construye la nueva imagen
# 2. La sube a GCR
# 3. Actualiza el servicio sin downtime
```

---

## Configuraciones Avanzadas

### Cambiar Region (Más cerca de Latinoamérica)

Edita `deploy.sh` y cambia:
```bash
REGION="southamerica-east1"  # São Paulo, Brasil
```

### Agregar Dominio Personalizado

1. Ve a Cloud Run Console: https://console.cloud.google.com/run
2. Click en tu servicio "fundamentos-digitales"
3. Pestaña "Manage Custom Domains"
4. Sigue las instrucciones para mapear tu dominio

### Configurar HTTPS Automático

Cloud Run provee **HTTPS automático** con certificados SSL gratuitos. No necesitas configurar nada.

---

## Costos Estimados

Cloud Run **Free Tier** incluye:
- 2 millones de requests/mes
- 360,000 GB-segundos de memoria/mes
- 180,000 vCPU-segundos/mes

**Para un ebook estático como este, probablemente nunca saldrás del free tier.**

Si excedes el free tier:
- ~$0.40 por millón de requests adicionales
- ~$0.0000025 por GB-segundo de memoria

**Ejemplo:** 100,000 visitas/mes = ~$0 (dentro del free tier)

---

## Troubleshooting

### Error: "Docker daemon not running"
```bash
# Inicia Docker Desktop manualmente
open -a Docker
```

### Error: "Permission denied: deploy.sh"
```bash
chmod +x deploy.sh
```

### Error: "Billing not enabled"
- Ve a https://console.cloud.google.com/billing
- Asocia una tarjeta de crédito (no se te cobrará si estás en free tier)

### Ver logs de Cloud Run
```bash
gcloud run services logs read fundamentos-digitales --region us-central1
```

---

## Alternativas Más Simples (Sin Docker)

Si quieres algo más simple sin necesidad de Docker ni gcloud CLI:

### Opción 1: Netlify (Drag & Drop)
1. Ve a https://www.netlify.com
2. Arrastra la carpeta `eternal-flare` al navegador
3. ¡Listo! Obtienes URL gratis + HTTPS

### Opción 2: Vercel
1. Ve a https://vercel.com
2. Importa el proyecto desde GitHub
3. Deploy automático en cada commit

### Opción 3: GitHub Pages (Gratis, sin límites)
```bash
# Crear repositorio en GitHub, luego:
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/tu-usuario/fundamentos-digitales.git
git push -u origin main

# Habilitar GitHub Pages en la configuración del repo
# Tu ebook estará en: https://tu-usuario.github.io/fundamentos-digitales
```

---

## ¿Qué opción elegir?

- **Cloud Run**: Mejor si quieres aprender GCP, escalar automáticamente, o tener control total
- **Netlify/Vercel**: Mejor si quieres simplicidad máxima (drag & drop)
- **GitHub Pages**: Mejor si es un proyecto open source y quieres hosting gratis ilimitado

Para un ebook estático, **GitHub Pages es probablemente la opción más simple y gratuita**.
