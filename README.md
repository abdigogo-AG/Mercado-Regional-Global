# 📦 Mercado Regional Global — Plataforma E-Commerce & ERP para Cooperativas

> **Proyecto:** Sistema de Comercio Electrónico y Gestión Empresarial Transfronteriza (México – EE. UU.)  
> **Grupo:** 502-A · Equipo VI  
> **Metodología:** Scrumban (Scrum + Kanban) con Iteraciones de 1 Semana  
> **Estándar de Documentación:** IEEE Std 830-1998 / ISO/IEC/IEEE 29148  


---

## 📌 1. Descripción General del Proyecto

**Mercado Regional Global** es una plataforma web integral que combina e-commerce y ERP ligero, diseñada para conectar a cooperativas productoras mexicanas con compradores locales e internacionales (iniciando con Estados Unidos en USD). 

El sistema permite a las cooperativas descentralizadas gestionar sus productos y existencias en tiempo real, ofreciendo a los compradores un catálogo multimoneda con reserva temporizada de inventario y simulación de logística transfronteriza.

```
┌─────────────────┐       ┌──────────────────────────┐       ┌─────────────────┐
│   Comprador     │ ────> │  Mercado Regional Global │ <──── │   Cooperativas  │
│ (MXN / USD)     │       │     (Backend Core)       │       │  (Ventas/Stock) │
└─────────────────┘       └──────────────────────────┘       └─────────────────┘
                                       │
                                       ▼
                          ┌──────────────────────────┐
                          │   Logística & Admin ERP  │
                          │   (Tracking / Tasa USD)  │
                          └──────────────────────────┘
```

---

## 🛠️ 2. Marco de Desarrollo y Dinámica de Trabajo

Para replicar un entorno de ingeniería de software real, el proyecto se construyó interactuando de forma asíncrona con un **Cliente Virtual Simulado mediante Inteligencia Artificial (LLM)**. El comportamiento y los parámetros operativos de la contraparte fueron configurados según la siguiente matriz de caracterización:

* **Perfil del Cliente:** Directora Comercial (*Carmen Santiago*).
* **Actitud del Cliente:** **Poco disponible** (respuestas breves y asíncronas por canal de comunicación/Slack).
* **Escala del Proyecto:** **Enorme** (múltiples áreas, roles diferidos e integración de comercio transfronterizo).
* **Estabilidad de Requerimientos:** **Evolutivos** (incorporación de reglas de negocio adicionales al revisar propuestas).

### Estrategia de Mitigación (Scrumban + Timeboxing)
Para garantizar entregas continuas en un entorno de comunicación asíncrona y alta incertidumbre:
1. **Timeboxing:** Ciclos de desarrollo semanales con objetivos fijos de entrega.
2. **Protocolo de Supuestos de Diseño:** Registro explícito de decisiones de arquitectura cuando las respuestas del cliente se encuentran pendientes.
3. **Control de Modificaciones en Jira:** Clasificación de requerimientos entre *Confirmados* y *Pendientes de Negociación*.

---

## 🚀 3. Funcionalidades Principales (Fase 1 / MVP)

### 🛒 Módulo de Comercio y Existencias
* **Catálogo Descentralizado:** Cada cooperativa administra de forma independiente sus existencias y productos.
* **Reserva Temporizada de Stock (12 Horas):** Algoritmo de apartado atómico de inventario en BD al generar una orden. Liberación automática si el pago no se realiza en la ventana de tiempo.
* **Gestión de Pagos Tardíos:** Flujo automático `REASIGNACION_STOCK` para atender compras cuyo pago se acredita posterior a la liberación de existencias.

### 💵 Arquitectura Multimoneda e Histórico
* **Captura Manual de Tipo de Cambio:** El Administrador registra la tasa oficial de prueba (MXN/USD) junto con su fecha de vigencia.
* **Inalterabilidad de Transacciones (*Snapshot*):** Cada orden congela el precio unitario, la divisa elegida y la tasa de cambio utilizada. Cambios futuros en el tipo de cambio no modifican transacciones pasadas.

### 🔒 Privacidad de Datos y Permisos (RBAC)
* **Segregación Estricta de Funciones (4 Roles):** Comprador, Cooperativa, Logística y Administrador.
* **Enmascaramiento de PII:** La interfaz de la cooperativa muestra únicamente *Nombre Completo* y *Dirección de Entrega*. Los datos bancarios, correos y teléfonos del comprador permanecen ocultos.

### 🌐 Simulación Internacional (Sandbox)
* **Gateway de Pagos Simulado:** Módulo Sandbox que procesa cobros ficticios en USD y MXN.
* **Cálculo de Envío con Tarifa Plana:** Simulación de costos por región y generación de guías de rastreo.
* **Distintivos de Simulación:** Identificación explícita en la interfaz con la etiqueta visual `"MÓDULO SIMULADO / SANDBOX"` y leyenda aclaratoria *"Sujeto a impuestos de importación en destino"*.

---

## 📊 4. Métricas de Calidad de Software

El proyecto monitorea el desempeño del software y la velocidad del equipo a través de 5 métricas cuantitativas:

| Métrica | Objetivo / Umbral | Herramienta / Método |
| :--- | :--- | :--- |
| **Cobertura de Pruebas Unitarias** | $\ge 75\%$ global ($\ge 90\%$ en reserva y divisas) | Jest / PyTest + Bitbucket Pipelines |
| **Densidad de Defectos** | $< 0.5$ bugs por Historia de Usuario | Jira Software (Bugs vs Stories) |
| **Lead Time de Pull Requests** | $< 24$ horas por revisión de código | Bitbucket Insights |
| **Concurrencia en Inventario** | **0.0% sobrentregas** (*over-selling*) | Pruebas de carga atómicas con JMeter |
| **Cumplimiento de Sprint Goal** | $\ge 85\%$ de historias completadas | Jira Burndown Chart |

---

## 🏗️ 5. Arquitectura del Sistema

El sistema utiliza una arquitectura monolítica modular de 3 capas:

1. **Capa de Presentación (Frontend):** Interfaz web responsiva con adaptación dinámica de componentes según el JWT del usuario autenticado.
2. **Capa de Negocio (Backend API):** Controladores RESTful con validación de entradas, transacciones atómicas de base de datos y tareas en segundo plano (*workers*) para vencimientos de inventario.
3. **Capa de Datos (Database):** Esquema relacional optimizado con restricciones de unicidad, tablas de tipos de cambio paramétricas y auditoría transaccional.

---

## 📋 6. Estructura del Repositorio y Documentación

* `/docs` — Documentación técnica oficial bajo el estándar IEEE 830 (SRS, Métricas, Plan de Desarrollo y Minutas).
* `/src/backend` — API RESTful, lógica de negocio y simuladores Sandbox.
* `/src/frontend` — Vistas web para Comprador, Cooperativa, Logística y Admin.
* `/tests` — Pruebas unitarias, de integración y scripts de concurrencia.

---

## 📄 Declaración de Uso de Inteligencia Artificial

De acuerdo con las normativas del desarrollo del proyecto:

> *"Para la elaboración de la documentación y estructuración de este proyecto se utilizó ChatGPT (OpenAI) y Gemini (Google) como apoyo en la organización de requerimientos según el estándar IEEE 830, simulación de la interlocución asíncrona del cliente y refinamiento de especificaciones técnicas. El equipo de desarrollo ha revisado, editado y comprobado todo el contenido, asumiendo la plena responsabilidad del producto final."*
