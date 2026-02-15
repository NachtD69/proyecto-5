# Telecom Pricing Plan Revenue Analysis

> Análisis estadístico de ingresos por plan de tarifas para optimizar presupuesto publicitario

![Python](https://img.shields.io/badge/Python-3.9-blue)
![Pandas](https://img.shields.io/badge/Pandas-1.x-green)
![NumPy](https://img.shields.io/badge/NumPy-1.x-013243)
![Matplotlib](https://img.shields.io/badge/Matplotlib-3.x-orange)
![SciPy](https://img.shields.io/badge/SciPy-1.x-8CAAE6)
![Status](https://img.shields.io/badge/Status-Completed-success)

##  Descripción

Análisis de datos para **Megaline**, operador de telecomunicaciones, con el objetivo de determinar cuál de sus dos planes de prepago (**Surf** vs **Ultimate**) genera más ingresos. Los resultados impactarán directamente la **asignación del presupuesto publicitario**.

Este proyecto combina análisis de comportamiento de usuarios, cálculo de ingresos por plan, y **pruebas de hipótesis estadísticas** para tomar decisiones basadas en datos.

### Contexto de negocio
El departamento comercial necesita:
- Comparar ingresos entre planes Surf y Ultimate
- Analizar patrones de uso de 500 clientes durante 2018
- Identificar qué plan genera más valor
- Justificar la inversión publicitaria con evidencia estadística

##  Objetivos del proyecto

1. **Análisis de comportamiento** - Entender cómo usan cada plan los clientes
2. **Cálculo de ingresos** - Determinar ingresos reales por cliente considerando excedentes
3. **Comparación estadística** - Probar hipótesis sobre diferencias de ingresos
4. **Recomendación de negocio** - Definir en qué plan invertir presupuesto publicitario

##  Estructura de tarifas

### Plan **Surf** ($20/mes)
- **500 minutos** incluidos
- **50 SMS** incluidos
- **15 GB** de datos incluidos

**Excedentes:**
-  $0.03/minuto adicional
-  $0.03/SMS adicional
-  $10/GB adicional

### Plan **Ultimate** ($70/mes)
-  **3000 minutos** incluidos
-  **1000 SMS** incluidos
-  **30 GB** de datos incluidos

**Excedentes:**
-  $0.01/minuto adicional
-  $0.01/SMS adicional
-  $7/GB adicional

###  Reglas de redondeo
- **Llamadas:** Cada llamada individual se redondea a minutos completos
- **Datos:** El total mensual se redondea hacia arriba a GB completos
- **SMS:** Se cuentan unidades completas

##  Estructura de datos

El dataset consta de **5 tablas relacionadas:**

### 1. `users` - Información de clientes
| Columna | Descripción |
|---------|-------------|
| `user_id` | ID único del usuario |
| `first_name` | Nombre del usuario |
| `last_name` | Apellido del usuario |
| `age` | Edad en años |
| `reg_date` | Fecha de registro |
| `churn_date` | Fecha de baja (null = activo) |
| `city` | Ciudad de residencia |
| `plan` | Plan contratado (Surf/Ultimate) |

### 2. `calls` - Registro de llamadas
| Columna | Descripción |
|---------|-------------|
| `id` | ID de la llamada |
| `call_date` | Fecha de la llamada |
| `duration` | Duración en minutos |
| `user_id` | ID del usuario |

### 3. `messages` - Registro de SMS
| Columna | Descripción |
|---------|-------------|
| `id` | ID del mensaje |
| `message_date` | Fecha del SMS |
| `user_id` | ID del usuario |

### 4. `internet` - Sesiones de datos
| Columna | Descripción |
|---------|-------------|
| `id` | ID de la sesión |
| `mb_used` | MB consumidos |
| `session_date` | Fecha de la sesión |
| `user_id` | ID del usuario |

### 5. `plans` - Detalles de planes
| Columna | Descripción |
|---------|-------------|
| `plan_name` | Nombre del plan |
| `usd_monthly_fee` | Cuota mensual |
| `minutes_included` | Minutos incluidos |
| `messages_included` | SMS incluidos |
| `mb_per_month_included` | Datos incluidos (MB) |
| `usd_per_minute` | Precio minuto extra |
| `usd_per_message` | Precio SMS extra |
| `usd_per_gb` | Precio GB extra |

## 🛠️ Tecnologías utilizadas

- **Python 3.x** - Lenguaje de programación
- **Pandas** - Manipulación de DataFrames y agregaciones
- **NumPy** - Cálculos numéricos y redondeos
- **Matplotlib** - Visualización de distribuciones de ingresos
- **SciPy** - Pruebas de hipótesis estadísticas (t-test)
- **Jupyter Notebook** - Desarrollo y documentación

## 🔍 Metodología de análisis

### Fase 1: Preprocesamiento
```python
# Cargar las 5 tablas
users = pd.read_csv('users.csv')
calls = pd.read_csv('calls.csv')
messages = pd.read_csv('messages.csv')
internet = pd.read_csv('internet.csv')
plans = pd.read_csv('plans.csv')

# Limpieza y preparación
- Conversión de fechas
- Manejo de valores nulos
- Agregación mensual por usuario
```

### Fase 2: Cálculo de consumo mensual
```python
# Por cada usuario y mes:
1. Sumar minutos (aplicando redondeo por llamada)
2. Contar SMS
3. Sumar MB y convertir a GB (redondeo hacia arriba)
```

### Fase 3: Cálculo de ingresos
```python
def calcular_ingreso(usuario, mes, plan):
    ingreso = plan['cuota_base']
    
    # Calcular excedentes
    if minutos > plan['minutos_incluidos']:
        ingreso += (minutos - plan['minutos_incluidos']) * plan['precio_minuto']
    
    if sms > plan['sms_incluidos']:
        ingreso += (sms - plan['sms_incluidos']) * plan['precio_sms']
    
    if gb > plan['gb_incluidos']:
        ingreso += (gb - plan['gb_incluidos']) * plan['precio_gb']
    
    return ingreso
```

### Fase 4: Análisis estadístico
```python
from scipy import stats

# Hipótesis nula: No hay diferencia en ingresos promedio
# Hipótesis alternativa: Hay diferencia significativa

ingresos_surf = ...
ingresos_ultimate = ...

t_stat, p_value = stats.ttest_ind(ingresos_surf, ingresos_ultimate)

# Si p_value < 0.05 → Rechazamos hipótesis nula
```

##  Hipótesis probadas

### Hipótesis 1: Comparación de planes
**H₀:** El ingreso promedio de Surf es igual al de Ultimate  
**H₁:** Los ingresos promedio son diferentes

### Hipótesis 2: Diferencias regionales (opcional)
**H₀:** No hay diferencia de ingresos entre regiones  
**H₁:** Existen diferencias significativas por región

##  Aprendizajes clave

- **Análisis de ingresos** - Cálculo de revenue considerando tarifas complejas
- **Pruebas estadísticas** - t-test para comparación de medias
- **SciPy** - Primera experiencia con librería estadística
- **Lógica de negocio** - Implementar reglas de redondeo específicas
- **Decisiones data-driven** - Conectar análisis con estrategia de marketing
- **Agregaciones complejas** - groupby mensual por usuario y plan





##  Habilidades demostradas

- **Análisis de negocio** - Entender modelo de precios y calcular ingresos
- **Estadística inferencial** - Pruebas de hipótesis con scipy.stats
- **Python avanzado** - Funciones personalizadas para cálculos complejos
- **Agregaciones** - groupby con múltiples dimensiones (usuario, mes, plan)
- **Storytelling** - Conectar hallazgos estadísticos con decisiones de negocio
- **Visualización** - Comparar distribuciones de ingresos entre planes

##  Impacto de negocio

**Pregunta clave:** ¿En qué plan debe invertir Megaline su presupuesto publicitario?

**Metodología:** Análisis estadístico riguroso comparando ingresos reales de 500 clientes durante 2018.

**Resultado:** Recomendación basada en evidencia estadística (p-value) sobre qué plan maximiza ingresos.


**Complejidad:** Cálculo de ingresos con reglas de negocio complejas (redondeos, excedentes, tarifas diferenciadas).

---

**Proyecto:** Bootcamp Data Analysis - Análisis Estadístico  
**Autor:** Marcos - [@NachtD69](https://github.com/NachtD69)  
**Fecha:** 2026  
**Cliente:** Megaline (Telecomunicaciones)  
**Técnica:** Pruebas de hipótesis estadísticas (t-test)
