# react-demo

App React (Vite) mínima usada como demo para el pipeline de Jenkins definido en [ci-jenkins-setup](https://github.com/tonysantacruz/ci-jenkins-setup).

## Ejecutar en local

```
npm install
npm run dev
```

## Build de producción

```
npm run build
```

## Con Docker

```
docker build -t react-demo .
docker run -d --name react-demo-app -p 8082:80 react-demo
```

La app queda disponible en `http://localhost:8082` (servida por nginx, con fallback de rutas a `index.html` para el routing de la SPA).

## Pipeline de Jenkins

El `Jenkinsfile` define 3 stages:

1. **Build** — corre `npm install` y `npm run build` en un agente Docker (`node:20-alpine`).
2. **Docker Build** — construye la imagen (`react-demo:<build>` y `:latest`) usando un Dockerfile multi-stage (build con Node, runtime con nginx).
3. **Deploy** — para el contenedor anterior (`react-demo-app`) y levanta el nuevo en el puerto `8082`.

El job está configurado con `pollSCM` (revisa este repo cada ~2 minutos) y `disableConcurrentBuilds` para evitar despliegues simultáneos.
