¡Claro! Te dejo un README estéticamente mejorado, más claro y adaptado para el uso de **Node.js + Express + Sequelize (con PostgreSQL)**, eliminando las menciones a MongoDB y mejorando el formato para mayor legibilidad y profesionalismo:

---

# 🔥 Fire Load Calculator - Sistema de Cálculo de Carga de Fuego

**Aplicación web para el cálculo de carga de fuego**, basada en las normativas **IRAM 11910** y **NFPA**, diseñada para empresas de seguridad contra incendios.  
Incluye recomendaciones de sistemas de extinción, análisis de riesgo y gestión de clientes.

---

## 🚀 Tecnologías

- **Frontend:** React
- **Backend:** Node.js + Express
- **Base de Datos:** PostgreSQL (vía Sequelize ORM)
- **Despliegue:** Compatible con nubes como AWS, Azure, DonWeb Cloud

---

## ⚙️ Arquitectura General

### 📘 Frontend (React)

- Formulario para ingresar datos (material, masa, poder calorífico, volumen, temperatura, etc.)
- Dashboard interactivo con:
  - Visualización de resultados (en MJ)
  - Nivel de riesgo
  - Recomendaciones de sistemas de extinción
- Interfaces amigables para usuarios técnicos y vendedores

### 🧠 Backend (Node.js + Express + Sequelize)

- API RESTful para procesar datos y realizar cálculos
- Lógica basada en normativas IRAM 11910 y NFPA
- Base de datos relacional para:
  - Históricos de cálculos
  - Información de clientes
  - Configuración de materiales
- Control de acceso por **roles**:
  - Administrador
  - Técnico
  - Vendedor

---

## 🔥 Cálculo de Carga de Fuego

Fórmula:

```
Q = Σ (mᵢ × Hᵢ)
```

- **Q**: carga de fuego total (MJ)
- **mᵢ**: masa del material (kg)
- **Hᵢ**: poder calorífico del material (MJ/kg)

---

## 📁 Estructura del Proyecto

```
fire-load-app/
├── backend/
│   ├── server.js
│   ├── routes/
│   │   └── fireLoadRoutes.js
│   ├── controllers/
│   │   └── fireLoadController.js
│   ├── models/
│   │   └── material.js (modelo Sequelize)
│   └── config/
│       └── database.js
├── frontend/
│   ├── src/
│   │   ├── App.js
│   │   ├── components/
│   │   │   ├── FireLoadForm.js
│   │   │   ├── FireLoadResult.js
│   │   │   └── Dashboard.js
│   │   └── services/
│   │       └── fireLoadService.js
│   └── public/
├── .env
├── package.json
└── README.md
```

---

## 💻 Backend (Sequelize + PostgreSQL)

### 🔸 Modelo - `models/material.js`

```js
module.exports = (sequelize, DataTypes) => {
  const Material = sequelize.define('Material', {
    nombre: DataTypes.STRING,
    masa: DataTypes.FLOAT, // en kg
    poderCalorifico: DataTypes.FLOAT // en MJ/kg
  });
  return Material;
};
```

### 🔸 Controlador - `controllers/fireLoadController.js`

```js
const calculateFireLoad = async (req, res) => {
  const { materiales } = req.body;
  const cargaFuego = materiales.reduce((total, mat) => {
    return total + (mat.masa * mat.poderCalorifico);
  }, 0);
  res.json({ cargaFuego });
};

module.exports = { calculateFireLoad };
```

### 🔸 Rutas - `routes/fireLoadRoutes.js`

```js
const express = require('express');
const router = express.Router();
const { calculateFireLoad } = require('../controllers/fireLoadController');

router.post('/calculate', calculateFireLoad);

module.exports = router;
```

### 🔸 Servidor - `server.js`

```js
const express = require('express');
const app = express();
const fireLoadRoutes = require('./routes/fireLoadRoutes');
require('dotenv').config();

app.use(express.json());
app.use('/api/fireload', fireLoadRoutes);

const { sequelize } = require('./models');
const PORT = process.env.PORT || 5000;

sequelize.sync().then(() => {
  app.listen(PORT, () => {
    console.log(`Servidor corriendo en el puerto ${PORT}`);
  });
});
```

---

## 🖥️ Frontend (React)

### 🔹 Formulario de ingreso - `FireLoadForm.js`

```jsx
import { useState } from 'react';
import fireLoadService from '../services/fireLoadService';

const FireLoadForm = ({ setResult }) => {
  const [materiales, setMateriales] = useState([]);

  const handleSubmit = async (e) => {
    e.preventDefault();
    const result = await fireLoadService.calculateFireLoad(materiales);
    setResult(result.cargaFuego);
  };

  const addMaterial = () => {
    setMateriales([...materiales, { nombre: '', masa: 0, poderCalorifico: 0 }]);
  };

  const handleChange = (index, field, value) => {
    const updated = [...materiales];
    updated[index][field] = value;
    setMateriales(updated);
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Ingresar Materiales</h2>
      {materiales.map((mat, i) => (
        <div key={i}>
          <input type="text" placeholder="Material" onChange={(e) => handleChange(i, 'nombre', e.target.value)} />
          <input type="number" placeholder="Masa (kg)" onChange={(e) => handleChange(i, 'masa', parseFloat(e.target.value))} />
          <input type="number" placeholder="Poder Calorífico (MJ/kg)" onChange={(e) => handleChange(i, 'poderCalorifico', parseFloat(e.target.value))} />
        </div>
      ))}
      <button type="button" onClick={addMaterial}>Agregar Material</button>
      <button type="submit">Calcular</button>
    </form>
  );
};

export default FireLoadForm;
```

---

## ✅ Instalación

### Backend

```bash
cd backend
npm install
```

Instala también Sequelize y PostgreSQL:

```bash
npm install sequelize pg pg-hstore dotenv express
```

### Frontend

```bash
cd frontend
npx create-react-app .
npm install axios
```

---

## ▶️ Ejecución

### Levantar backend

```bash
node server.js
```

### Levantar frontend

```bash
npm start
```

---

## 🔎 Resultado Final

- Ingresás materiales, masa y poder calorífico.
- El backend calcula la carga total en MJ.
- El frontend muestra el resultado en un dashboard simple e interactivo.

---

## 🎯 Próximos pasos

- Comparar carga calculada con normas IRAM y NFPA.
- Sugerencias de sistemas de extinción según el riesgo.
- Guardar análisis históricos por cliente.
- Soporte multiusuario y multiempresa.

---

## 🔁 Flujo Básico

1. El vendedor ingresa los datos del cliente.
2. La API calcula la carga de fuego.
3. El sistema compara contra normas IRAM.
4. Se proponen mejoras y recomendaciones.
5. Se genera un informe detallado en el dashboard.

---

¿Querés que también te prepare este proyecto como un repositorio listo para GitHub, con estructura, licencia, descripción, y `README.md` incluido?
