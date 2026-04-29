# Tarea1SD

# Tarea 1 – Sistema distribuido de caché para consultas geoespaciales

Este proyecto implementa una plataforma distribuida basada en microservicios para optimizar consultas geoespaciales sobre el dataset **Google Open Buildings**, limitado a la Región Metropolitana de Santiago.

El sistema simula un flujo real de procesamiento de consultas mediante:
- Generador de tráfico (Zipf / Uniform)
- Proxy de caché con Redis
- Generador de respuestas en memoria
- Servicio de métricas

Todo el sistema está orquestado con **Docker Compose**, lo que permite su despliegue reproducible en cualquier entorno.

---

## 📁 Estructura del proyecto

tarea1sdv2/
├── Servidor/                # Proxy de caché (Node.js + Redis)
├── Generador-respuestas/    # Motor de consultas en memoria
├── Metricas/                # Servicio de métricas y logging
├── Generador-trafico/       # Simulador de carga (Zipf / Uniform)
├── data/                    # Dataset Open Buildings
├── metrics_data/            # Persistencia de métricas (CSV)
├── docker-compose.yml
└── README.md

---

## ⚙️ Requisitos

- Docker ≥ 20.10
- Docker Compose ≥ 2.x
- Git
- (Opcional) Python 3.9+ para visualización de resultados

---

## 🚀 Instalación y ejecución

### 1. Clonar el repositorio

git clone https://github.com/TU_USUARIO/tarea1sdv2.git
cd tarea1sdv2

### 2. Agregar dataset

data/open_buildings_v3_points_your_own_wkt_polygon.csv

### 3. Ejecutar sistema

docker-compose up --build

### 4. Detener sistema

docker-compose down

Para limpiar completamente:

docker-compose down -v

---

## 🧪 Experimentos

Zipf (default):
docker-compose up

Uniforme:
DISTRIBUTION=uniform docker-compose up

CACHE_SIZE=50mb docker-compose up
CACHE_SIZE=200mb docker-compose up
CACHE_SIZE=500mb docker-compose up

EVICTION_POLICY=allkeys-lfu docker-compose up

TTL=60 docker-compose up

---

## 📊 Métricas

http://localhost:4000/metrics

CSV:
http://localhost:4000/metrics/csv

Reset:
POST http://localhost:4000/metrics/reset

---

## 📈 Script de gráficos

python3 graficar_resultados.py

---

## 🧠 Notas

- conf_min varía entre 0.0 y 1.0
- eficiencia = (hits * t_cache - misses * t_dh) / total
- métricas se guardan en metrics.csv

---

## 👤 Autor

Felipe – Sistemas Distribuidos 2026-1
