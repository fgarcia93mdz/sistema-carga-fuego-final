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

Estructura del Proyecto
pgsql
Copiar
Editar
fire-load-app/
│
├── backend/
│   ├── server.js
│   ├── routes/
│   │   └── fireLoadRoutes.js
│   ├── controllers/
│   │   └── fireLoadController.js
│   └── models/
│       └── material.js
│
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
│
├── .env
├── package.json
├── README.md
└── .gitignore
Backend (Node.js + Express + MongoDB)
1. Modelo para Material (models/material.js)
javascript
Copiar
Editar
const mongoose = require('mongoose');

const materialSchema = new mongoose.Schema({
  nombre: String,
  masa: Number, // en kg
  poderCalorifico: Number // en MJ/kg
});

module.exports = mongoose.model('Material', materialSchema);
2. Controlador (controllers/fireLoadController.js)
javascript
Copiar
Editar
const Material = require('../models/material');

// Cálculo de carga de fuego: Q = Σ (m_i * H_i)
const calculateFireLoad = async (req, res) => {
  const { materiales } = req.body;

  const cargaFuego = materiales.reduce((total, mat) => {
    return total + (mat.masa * mat.poderCalorifico);
  }, 0);

  res.json({ cargaFuego });
};

module.exports = { calculateFireLoad };
3. Ruta (routes/fireLoadRoutes.js)
javascript
Copiar
Editar
const express = require('express');
const router = express.Router();
const { calculateFireLoad } = require('../controllers/fireLoadController');

router.post('/calculate', calculateFireLoad);

module.exports = router;
4. Servidor (server.js)
javascript
Copiar
Editar
const express = require('express');
const mongoose = require('mongoose');
const fireLoadRoutes = require('./routes/fireLoadRoutes');
require('dotenv').config();

const app = express();

app.use(express.json());
app.use('/api/fireload', fireLoadRoutes);

mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('MongoDB conectado'))
  .catch(err => console.error(err));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Servidor corriendo en el puerto ${PORT}`));
Frontend (React)
5. Formulario para ingresar datos (components/FireLoadForm.js)
javascript
Copiar
Editar
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
    const updatedMaterials = [...materiales];
    updatedMaterials[index][field] = value;
    setMateriales(updatedMaterials);
  };

  return (
    <div>
      <h2>Ingresar Materiales</h2>
      {materiales.map((mat, index) => (
        <div key={index}>
          <input 
            type="text" 
            placeholder="Material" 
            onChange={(e) => handleChange(index, 'nombre', e.target.value)} 
          />
          <input 
            type="number" 
            placeholder="Masa (kg)" 
            onChange={(e) => handleChange(index, 'masa', parseFloat(e.target.value))} 
          />
          <input 
            type="number" 
            placeholder="Poder Calorífico (MJ/kg)" 
            onChange={(e) => handleChange(index, 'poderCalorifico', parseFloat(e.target.value))} 
          />
        </div>
      ))}
      <button onClick={addMaterial}>Agregar Material</button>
      <button onClick={handleSubmit}>Calcular Carga de Fuego</button>
    </div>
  );
};

export default FireLoadForm;
6. Servicio para conectar con la API (services/fireLoadService.js)
javascript
Copiar
Editar
import axios from 'axios';

const API_URL = 'http://localhost:5000/api/fireload/calculate';

const calculateFireLoad = async (materiales) => {
  const response = await axios.post(API_URL, { materiales });
  return response.data;
};

export default { calculateFireLoad };
7. Dashboard para mostrar el resultado (components/FireLoadResult.js)
javascript
Copiar
Editar
const FireLoadResult = ({ result }) => {
  return (
    <div>
      <h3>Carga de Fuego Calculada:</h3>
      <p>{result} MJ</p>
    </div>
  );
};

export default FireLoadResult;
8. Componente Principal (App.js)
javascript
Copiar
Editar
import { useState } from 'react';
import FireLoadForm from './components/FireLoadForm';
import FireLoadResult from './components/FireLoadResult';

function App() {
  const [result, setResult] = useState(null);

  return (
    <div>
      <h1>Calculadora de Carga de Fuego</h1>
      <FireLoadForm setResult={setResult} />
      {result !== null && <FireLoadResult result={result} />}
    </div>
  );
}

export default App;
✅ Instalación de dependencias
En la carpeta backend:

bash
Copiar
Editar
npm init -y
npm install express mongoose cors dotenv
En la carpeta frontend:

bash
Copiar
Editar
npx create-react-app fire-load-frontend
✅ Ejecución
Levantar el backend:
bash
Copiar
Editar
node server.js
Levantar el frontend:
bash
Copiar
Editar
npm start
🔥 Resultado final:
Ingresás los materiales, masas y poder calorífico.
El backend calcula la carga de fuego.
El dashboard muestra la cantidad de megajoules (MJ).
🎯 Próximos pasos:
Implementar lógica para comparar con normativas IRAM y NFPA.
Agregar sugerencias de sistemas de extinción.
Guardar datos históricos en la base de datos.

Flujo básico:
El vendedor ingresa datos del cliente (materiales, área, tipo de sistema actual).
La API calcula la carga de fuego y compara con las normativas IRAM.
El sistema sugiere sistemas de extinción (aspersores, hidrantes, etc.) o mejoras en la protección pasiva.
El dashboard muestra un informe detallado y recomendaciones.
