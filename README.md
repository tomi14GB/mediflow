# MediFlow — Arquitectura de Microservicios

Solución de la Evaluación Parcial N°3 — **DSY1106 Desarrollo Fullstack III**.

MediFlow moderniza un sistema clínico monolítico (que generaba caídas los lunes
por la mañana y arrastraba una base de datos de 2 TB) hacia una arquitectura de
microservicios desacoplada.

## Componentes

| Componente | Tecnología | Puerto | Persistencia |
|---|---|---|---|
| `frontend-mediflow` | React 18 (Custom Hooks + Provider) | 3000 | — |
| `bff-mediflow` | Node.js + Express | 8080 | — (agregador) |
| `ms-core-hce` | Java 17 + Spring Boot | 8081 | PostgreSQL vía **JPA** |
| `ms-laboratorios` | Java 17 + Spring Boot | 8082 | PostgreSQL vía **Procedimientos Almacenados** |

## Arquitectura

```
React (Portal)  →  BFF (Express)  →  ┌─ MS Core HCE      → PostgreSQL (JPA)
                                     └─ MS Laboratorios  → PostgreSQL (SPs)
```

El **BFF** es el único punto de entrada del frontend: agrega las respuestas de
ambos microservicios en una sola llamada, reduciendo la latencia de red y
evitando que la interfaz conozca la topología interna del backend.

## Orden de ejecución

1. Levantar PostgreSQL (dos bases: `mediflow_hce` y `mediflow_lab`).
2. `ms-core-hce`: `mvn spring-boot:run` (puerto 8081).
3. `ms-laboratorios`: `mvn spring-boot:run` (puerto 8082).
4. `bff-mediflow`: `npm install && npm start` (puerto 8080).
5. `frontend-mediflow`: `npm install && npm start` (puerto 3000).

## Pruebas y cobertura

Cada componente supera el mínimo del 60% exigido. Ver `docs/informe-pruebas.pdf`.

| Componente | Cobertura líneas | Pruebas |
|---|---|---|
| ms-core-hce | ≥ 60% (JaCoCo) | 11 |
| ms-laboratorios | ≥ 60% (JaCoCo) | 10 |
| bff-mediflow | 85.7% (Jest) | 8 |
| frontend-mediflow | 91.2% (Jest) | 11 |
