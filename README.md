# Liquidación de Sueldos — Demo CI/CD

Demo para clase de CI/CD: una app simple de **liquidación de sueldos**, dividida en
**backend (FastAPI)** y **frontend (React + Vite)**, con pipelines independientes en
**GitHub Actions** que corren tests/build y despliegan a **Render** (API) y **Netlify** (UI).

[![Tests](https://github.com/tinxo/IS3-Demo-ci-cd/actions/workflows/python-test.yml/badge.svg)](https://github.com/tinxo/IS3-Demo-ci-cd/actions/workflows/python-test.yml)

## Estructura

```
.
├── .github/                 # Actions para GitHUb
│   ├── workflows/
│   │   ├── python-test-and-lint.yml   # Action para test del backend
├── backend/                 # API FastAPI
│   ├── app/
│   │   ├── liquidacion.py   # Lógica de negocio (clase Liquidacion)
│   │   └── main.py          # Endpoints FastAPI
│   ├── tests/
│   │   ├── test_liquidacion.py        # Tests unitarios
│   │   └── test_api_integration.py    # Tests de integración (TestClient)
│   ├── requirements.txt
│
├── frontend/                 # UI React + Vite
│   └── src/
│       ├── App.jsx
│       └── index.css
```

## Cómo ejecutar localmente

### Backend

Con UV:  
```bash
cd backend
uv uv sync
uv uv run uvicorn app.main:app --reload --port 8000
```

Con pip:  
```bash
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

Tests (habiendo cargado el entorno):

```bash
cd backend
pytest tests/ -v
```

### Frontend

```bash
cd frontend
npm install
cp .env.example .env   # ajustar VITE_API_URL con la URL de la API en Render
npm run dev
```

## Ejecución del deploy

### En Render
1. Crear un **Web Service** nuevo, conectado al repo.
2. Root directory: `backend`.
3. Build command: `uv sync --frozen && uv cache prune --ci`.
4. Start command: `uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000`.
5. Verificar que la configuración de **Auto Deploy** esté en "**On commit**".

### En Netlify
1. Crear un sitio nuevo vinculando desde el repo.
2. En la configuración establecer una variable de entorno, puede ser a través del .env (si fue generado) o cargando ahí la variable `VITE_API_URL` con la URL de la API en Render.
3. Estableder en la configuración el Base directory: `frontend`.
4. Build command: `npm run build`
5. Publish directory: `frontend/dist`


## Actions

Para replicar un flujo de CI para el backend se agregó un archivo de un action que realiza la ejecución de tests (definidos en el directorio `backend/tests`) para verificar el código de la API.
