# Calabria Renewables - Environmental Monitoring Platform

Welcome to the **Calabria Renewables** platform, a comprehensive web application designed for the visualization and management of meteorological and environmental data. This platform allows users to upload, process, analyze, and impute campaign data from various observation stations.

## 🚀 Key Features

*   **Interactive Dashboard**: Real-time visualisation of environmental metrics with dynamic statistics and interactive maps.
*   **Campaign Management**: 
    *   Create, update, and delete monitoring campaigns.
    *   **Robust Identification**: Uses a composite key (Name, Latitude, Longitude, Measurement Instrument) to uniquely identify campaigns.
    *   **Auto-Append**: Intelligently appends new file uploads to existing campaigns if headers and composite keys match, streamlining data ingestion.
*   **Data Processing Pipeline**:
    *   Supports upload of `.csv`, `.xlsx`, `.xls`, and `.ods` files.
    *   **Iterative Processing**: Efficiently handles large `.ods` files using iterative, chunk-based conversion strategies to minimise memory overhead.
    *   **Intelligent Parsing**: Automatically detects "Timestamp" columns for time-series alignment.
    *   **Python Integration**: Uses dedicated Python scripts (`convert_to_csv.py`, `impute_data.py`) for robust data cleaning, format standardisation, and transformation before ingestion.
*   **Data Imputation**:
    *   Ability to estimate missing metric values using time-based interpolation algorithms.
    *   Distinctly visualises imputed data points on charts for clear differentiation from raw measurements.
*   **Advanced Visualization**:
    *   **Time Series Charts**: Dynamic line charts representing metric trends over selected time windows, highlighting imputed data.
    *   **Geospatial Mapping**: Integrated maps (Leaflet/Mapbox) to show station locations and status.
    *   **Smart Stats**: Automatically maps metrics to relevant indicators.
*   **User Authentication**: Secure Login/Logout with JWT-based session management.

## 🛠️ Technology Stack

The platform is built using a modern **MERN** (MySQL/SQLite, Express, React, Node) architecture with robust Python integration for data science tasks.

### Frontend
*   **Framework**: [React](https://react.dev/) (v18)
*   **Build Tool**: [Vite](https://vitejs.dev/) for blazing fast development.
*   **Styling**: [Tailwind CSS](https://tailwindcss.com/) for a sleek, responsive, and modern UI.
*   **Visualization**:
    *   [Recharts](https://recharts.org/) for data plotting.
    *   [Lucide React](https://lucide.dev/) for beautiful icons.
    *   [Leaflet](https://leafletjs.com/) & `react-leaflet` / `react-map-gl` for maps.
*   **State Management**: React Hooks (`useState`, `useEffect`) and Context.

### Backend
*   **Runtime**: [Node.js](https://nodejs.org/)
*   **Web Framework**: [Express.js](https://expressjs.com/)
*   **Database ORM**: [Prisma](https://www.prisma.io/) (Handling database querying and schema management).
*   **Data Processing**:
    *   **Python (Pandas)**: Used for heavy-lifting data conversion, missing value imputation (`impute_data.py`), and iterative processing of large datasets.
    *   **CSV-Parser**: For efficient, streaming ingestion of large datasets into the database.
*   **Real-time**: [Socket.io](https://socket.io/) for upload progress tracking.
*   **Authentication**: `jsonwebtoken` (JWT) and `bcryptjs`/`bcrypt`.

## ⚙️ How It Works

### 1. Data Ingestion Workflow
1.  **Upload**: Users upload spreadsheet files via the Dashboard.
2.  **Conversion**: The backend spawns a Python subprocess to:
    *   Iteratively convert Excel/ODS formats to standard CSV without memory bloat.
    *   **Prioritise "Timestamp"**: Scans columns to identify the primary time axis.
    *   Extract metadata (Start Date, End Date).
3.  **Conflict Resolution**: Applies composite key and header validation logic to either prompt the user for a merge, auto-append data, or reject the upload on schema mismatch.
4.  **Streaming Import**: The clean CSV is streamed into the database using Prisma `createMany` in batches.

### 2. Dashboard Logic
*   **Metric Selection**: Users select a variable (e.g., Temperature, Wind Speed).
*   **Data Imputation**: Users can opt to impute missing data, which triggers a backend script (`impute_data.py`) that applies interpolation and persists the results. The chart updates dynamically to reflect this.
*   **Visualisation**: The `CampaignChart` component queries the API, filtering by the "Timestamp" column to generate accurate time-series plots.

## 📂 Project Structure

```bash
Calabria Renewables/
├── frontend/           # React Application
│   ├── src/
│   │   ├── components/ # Reusable UI components (Navbar, StatsCard, Charts)
│   │   ├── pages/      # Main application pages (Dashboard, Login)
│   │   └── api/        # Axios configuration
│   └── package.json
├── backend/            # Express Server
│   ├── src/
│   │   ├── controllers/# Business logic (Auth, Campaign management)
│   │   ├── routes/     # API Endpoints
│   │   ├── scripts/    # Python tools (convert_to_csv.py, impute_data.py)
│   │   └── server.js   # Server Entry
│   ├── prisma/         # Database Schema
│   └── package.json
├── docker-compose.yml  # Docker deployment configuration
├── ods_to_csv_converter.py 
└── README.md           # This file
```

## 🐳 Docker Deployment (Recommended)

The easiest way to run the entire platform (Frontend, Backend, and PostgreSQL database) is using Docker Compose.

### Prerequisites for Docker
*   [Docker](https://docs.docker.com/get-docker/) installed.
*   [Docker Compose](https://docs.docker.com/compose/install/) installed.

### Running with Docker

1.  Make sure you are in the root directory (where `docker-compose.yml` is located).
2.  Start the services in detached mode:
    ```bash
    docker-compose up --build -d
    ```
3.  **Access the application**:
    *   **Frontend**: Open `http://localhost` (or the IP of your Docker host) in your browser.
    *   **Backend API**: Running on `http://localhost:5000`.
    *   **Database**: PostgreSQL is exposed internally to the backend, but its port is also mapped to `5432` on your host.

4.  **Stopping the platform**:
    ```bash
    docker-compose down
    ```
    *Note: The database data is persisted in a Docker volume (`pgdata`), so your data won't be lost when you stop the containers.*

## 🚀 Getting Started (Manual Setup)

### Prerequisites
*   Node.js (v18+)
*   Python (3.8+) with `pandas` installed (`pip install pandas openpyxl odfpy`)
*   npm or yarn

### Installation

1.  **Backend Setup**:
    ```bash
    cd backend
    npm install
    npx prisma generate
    npx prisma migrate dev --name init  # Initialize Database
    npm run dev
    ```

2.  **Frontend Setup**:
    ```bash
    cd frontend
    npm install
    npm run dev
    ```

3.  **Access**: Open `http://localhost:5173` (or your configured port) in your browser.

---
*Generated by Antigravity Assistant*
