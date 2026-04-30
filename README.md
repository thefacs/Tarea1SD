# Sistema Distribuido de Caché para Consultas Geoespaciales

Este proyecto implementa un sistema distribuido de caché sobre el dataset **Google Open Buildings** (Región Metropolitana de Santiago), utilizando una arquitectura basada en microservicios con Docker.

---

## Arquitectura del sistema

El sistema está compuesto por los siguientes microservicios:

| Servicio                  | Puerto | Función                                   |
|--------------------------|--------|-------------------------------------------|
| Generador de tráfico     | —      | Genera consultas (Zipf / Uniforme)        |
| Proxy de caché           | 3000   | Implementa cache-aside con Redis          |
| Generador de respuestas  | 3001   | Procesamiento de consultas en memoria      |
| Servicio de métricas     | 4000   | Registro y análisis de desempeño           |
| Redis                    | 6379   | Almacenamiento en caché                   |

---

## A tener en cuenta

Debido al tamaño del dataset, no fue posible incluirlo en el repositorio de GitHub. Por esta razón, no se encuentra disponible la carpeta `data`.

Para ejecutar el proyecto correctamente, se debe crear manualmente una carpeta llamada `data` al mismo nivel que los demás módulos (Generador-respuestas, Generador-trafico, etc.).

Dentro de esta carpeta debe ubicarse el dataset con el siguiente nombre:

`open_buildings_v3_points_your_own_wkt_polygon`



## Ejecución de experimentos

Antes de ejecutar cada experimento, se recomienda limpiar el entorno:

```bash
docker-compose down -v
```

### 1. Distribución Zipf (base)
```bash
docker-compose up | tee resultados_zipf.txt
cp metrics_data/metrics.csv metrics_zipf.csv
```

### 2. Distribución uniforme
```bash
DISTRIBUTION=uniform docker-compose up | tee resultados_uniform.txt
cp metrics_data/metrics.csv metrics_uniform.csv
```

### 3. Caché 50 MB (Zipf)
```bash
CACHE_SIZE=50mb docker-compose up | tee resultados_cache_50mb.txt
cp metrics_data/metrics.csv metrics_cache_50mb.csv
```

### 4. Caché 500 MB (Zipf)
```bash
CACHE_SIZE=500mb docker-compose up | tee resultados_cache_500mb.txt
cp metrics_data/metrics.csv metrics_cache_500mb.csv
```

### 5. Política de evicción LFU (Zipf, 200 MB)
```bash
EVICTION_POLICY=allkeys-lfu docker-compose up | tee resultados_lfu.txt
cp metrics_data/metrics.csv metrics_lfu.csv
```

### 6. TTL = 60 segundos (Zipf)
```bash
TTL=60 docker-compose up | tee resultados_ttl_60.txt
cp metrics_data/metrics.csv metrics_ttl_60.csv
```

---

## Resultados principales

| Experimento | Hit Rate | Cache Efficiency | Evictions |
|-------------|----------|------------------|-----------|
| Zipf (200 MB, LRU)       | 61,17% | −2,86 ms | 0 |
| Uniform (200 MB, LRU)    | 56,83% | −7,83 ms | 0 |
| Zipf (50 MB)             | 63,67% | −1,68 ms | 0 |
| Zipf (500 MB)            | 62,50% | −2,27 ms | 0 |
| Zipf (LFU)               | 62,50% | −3,18 ms | 0 |
| Zipf (TTL 60s)           | 62,33% | −4,68 ms | 0 |

---

## Conclusiones

- Zipf presenta mejor rendimiento por mayor localidad de referencia.
- El tamaño de caché no afecta significativamente los resultados.
- LRU y LFU muestran comportamiento similar.
- TTL bajo reduce la eficiencia de caché.
- La eficiencia global es negativa en todos los casos.

---

## Observaciones

- Q4 es la consulta más sensible del sistema.
- El rendimiento depende principalmente de la localidad del tráfico.

---

## Mejoras futuras

- Cache warming
- TTL dinámico
- Mayor presión de memoria para evaluar reemplazo
  
## Integrantes

- Felipe Cuevas  
- Ariel Oyarce
