OBS: The source code will be available in this repository once the article is accepted.

# Quantifying the Hype: Search-Driven Financial Market Dynamics

This repository contains the source code and datasets for the manuscript **"Quantifying the Hype: Exploring Search-Driven Financial Market Dynamics via Machine Learning and Econometric Pipeline"** submitted to _IEEE Transactions on Computational Social Systems_.

## Overview

This project introduces a unified, API-driven computational framework for modeling and characterizing attention-driven collective behavior in financial systems. By automating the extraction and synchronization of online search interest and financial asset valuation, the platform dynamically evaluates the econometric relationship between market hype and price movements.

The analytical pipeline integrates:

- **Classical Econometrics:** Vector Autoregressive (VAR) modeling and Granger Causality tests to quantify linear temporal dynamics and directional predictability.
- **Information Theory:** Mutual Information (utilizing k-NN and KSG estimators) to detect complex, non-linear informational dependencies.
- **Machine Learning & Forecasting:** The Prophet additive algorithm for out-of-sample price projection and systematic backtesting against baseline models.

## Repository Structure

- `/backend`: Contains the FastAPI Python server orchestrating data extraction (interfacing with Alpha Vantage, Finnhub, and pytrends), temporal synchronization (forward-fill imputation and min-max normalization), and the core computational modeling scripts.
- `/frontend`: Contains the React and TypeScript Single Page Application (SPA), utilizing Echarts-for-react for rendering interactive, dense time-series visualizations.
- `/data`: Includes the exported datasets containing the exact time-series metrics used for the manuscript's empirical case studies.

## Data Availability

The `/data` directory contains the synchronized and normalized data utilized in the empirical evaluations. This includes the high-frequency daily and weekly observations for NVIDIA (NVDA), Bitcoin (BTC), and GameStop (GME) required to reproduce the statistical matrices and forecasting performance evaluations presented in the study.

## Getting Started

### Prerequisites

- Python 3.10 or newer and pip
- Node.js 18 or newer, plus Yarn (the frontend includes `yarn.lock`)
- API keys for Alpha Vantage and Finnhub
- Internet access for the live Finnhub, Alpha Vantage, and Google Trends requests

### 1. Configure the backend

From the repository root, create and activate a Python virtual environment in PowerShell:

```powershell
cd backend
py -3 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If PowerShell blocks activation, allow scripts for this terminal with `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`, then activate the environment again.

Create `backend/.env` with your provider keys:

```dotenv
ALPHA_VANTAGE_API_KEY3=your_alpha_vantage_key
FINNHUB_API_KEY=your_finnhub_key
```

The backend requires these exact variable names when it starts. Google Trends does not require a key. Keep real keys out of source control.

### 2. Start the backend

In the same activated PowerShell terminal, from the `backend` directory, run:

```powershell
python -m uvicorn api.main:app --reload --port 8080
```

The health endpoint is [http://localhost:8080/](http://localhost:8080/), and the interactive API documentation is [http://localhost:8080/docs](http://localhost:8080/docs). Leave this terminal running.

### 3. Configure and start the frontend

Open a second terminal at the repository root. Set the API URL in `frontend/.env.local`:

```dotenv
VITE_BACKEND_URL=http://localhost:8080
```

Then install dependencies and start Vite:

```powershell
cd frontend
yarn install
yarn dev
```

Open [http://localhost:3000](http://localhost:3000). Keep both the backend and frontend terminals running while using the app.

### Troubleshooting

- If `yarn` is not recognized, enable/install Yarn through Corepack for your Node.js installation, then rerun `yarn install`.
- If the backend reports a missing Finnhub or Alpha Vantage key, check `backend/.env` for the exact variable names above and restart Uvicorn.
- Forecast requests use Prophet. If Prophet fails because CmdStan is missing, run `python setup_prophet.py` from `backend` after installing the requirements. On Windows, CmdStan may also require a C++ build toolchain and permission to create a symbolic link.
- Alpha Vantage and Google Trends have usage limits. Repeated historical-data or forecast requests may be rate-limited even when the app is configured correctly.

### Production frontend build

From `frontend`, run `yarn build`. The generated static site is written to `frontend/dist`; this does not start the backend or deploy either service.
