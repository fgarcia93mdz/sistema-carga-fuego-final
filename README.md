# sistema-carga-fuego-final

Arquitectura de la app:
Frontend (React):

Formulario para ingresar datos como materiales, volumen del espacio, temperatura, etc.
Dashboard interactivo para mostrar resultados de carga de fuego y recomendaciones.
Visualización gráfica del nivel de riesgo y compatibilidad con distintos sistemas de extinción.
Backend (Node.js con Express):

API REST que procesa los datos y realiza los cálculos basados en la norma IRAM 11910 y NFPA.
Base de datos (por ejemplo, MongoDB o PostgreSQL) para almacenar históricos de cálculos y análisis de clientes.
Algoritmo que evalúa la carga de fuego y sugiere sistemas de extinción adecuados.
Cálculo de carga de fuego:

Fórmula estándar:
𝑄
=
∑
(
𝑚
𝑖
×
𝐻
𝑖
)
Q=∑(m 
i
​
 ×H 
i
​
 )
Donde 
𝑚
𝑖
m 
i
​
  es la masa del material y 
𝐻
𝑖
H 
i
​
  es el poder calorífico.
Seguridad y permisos:

Roles para administradores, técnicos y vendedores.
Gestión de clientes y seguimiento de riesgos.
Escalabilidad:

Implementación en la nube (por ejemplo, AWS o Azure).
API abierta para integrar con otros sistemas de gestión de incendios.
Flujo básico:
El vendedor ingresa datos del cliente (materiales, área, tipo de sistema actual).
La API calcula la carga de fuego y compara con las normativas IRAM.
El sistema sugiere sistemas de extinción (aspersores, hidrantes, etc.) o mejoras en la protección pasiva.
El dashboard muestra un informe detallado y recomendaciones.
