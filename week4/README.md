# Week 4: Data Wrangling & APIs

**Data Engineering Course · Pipeline Africa**

---

## Overview

This week you will build your first API pipeline: pull live weather data from the internet, clean and reshape it with pandas, save it to CSV, and load it into PostgreSQL — the full ingestion loop that appears in almost every real data engineering job.

---

## Learning Objectives

By the end of this week you will be able to:

1. Make HTTP requests to a REST API and handle the response
2. Parse nested JSON into a flat pandas DataFrame
3. Loop over multiple cities and combine the results
4. Handle pagination and rate limiting in an API client
5. Validate and clean API data
6. Load data into PostgreSQL from Python
7. Use environment variables for configuration
8. Add logging to a pipeline

---

## Schedule

| Session | Topic | Notebook |
|---------|-------|----------|
| 1 | REST APIs & the `requests` library | [lesson1_rest_apis.ipynb](lesson1_rest_apis.ipynb) |
| 2 | Data wrangling: JSON → DataFrame | [lesson2_data_wrangling.ipynb](lesson2_data_wrangling.ipynb) |
| 3 | Loading data into PostgreSQL | [lesson3_postgresql.ipynb](lesson3_postgresql.ipynb) |
| 4 | Logging & configuration | [lesson4_logging_config.ipynb](lesson4_logging_config.ipynb) |
| Lab | End-to-end weather pipeline | [labs/lab4_weather_pipeline.ipynb](labs/lab4_weather_pipeline.ipynb) |
| Practice | API exercises | [exercises/api_exercises.ipynb](exercises/api_exercises.ipynb) |
| Group | Discussion & review | [activities/week4_activities.ipynb](activities/week4_activities.ipynb) |

---

## The API: Open-Meteo

All lessons use the [Open-Meteo](https://open-meteo.com/) weather API. It is:

- **Free** — no account or API key needed
- **Reliable** — backed by national meteorological services
- **Realistic** — used in real projects

### Endpoints used this week

| Endpoint | Purpose |
|----------|---------|
| `https://api.open-meteo.com/v1/forecast` | 7-day forecast |
| `https://archive-api.open-meteo.com/v1/archive` | Historical data (the lab) |

### Example URL

```
https://api.open-meteo.com/v1/forecast
  ?latitude=6.5244
  &longitude=3.3792
  &daily=temperature_2m_max,temperature_2m_min,precipitation_sum,windspeed_10m_max
  &timezone=Africa/Lagos
```

---

## Cities Dataset

`data/cities.csv` contains five major African cities used throughout the week:

| City | Country | Latitude | Longitude |
|------|---------|----------|-----------|
| Lagos | Nigeria | 6.5244 | 3.3792 |
| Nairobi | Kenya | -1.2921 | 36.8219 |
| Accra | Ghana | 5.6037 | -0.1870 |
| Johannesburg | South Africa | -26.2041 | 28.0473 |
| Cairo | Egypt | 30.0444 | 31.2357 |

---

## Setup

### Python packages

```bash
pip install requests pandas psycopg2-binary python-dotenv
```

### PostgreSQL

You should have PostgreSQL installed from Week 1. If not, download it from [postgresql.org](https://www.postgresql.org/download/).

Create a database for this week:

```sql
CREATE DATABASE weather_db;
```

### Environment variables

Create a file called `.env` in the `week4/` folder:

```
DB_HOST=localhost
DB_PORT=5432
DB_NAME=weather_db
DB_USER=postgres
DB_PASSWORD=your_password_here
```

**Do not commit `.env` to GitHub.** It contains your database password.

---

## Deliverable

A working pipeline that:

1. Fetches one month of historical weather data for all five cities from the Open-Meteo archive API
2. Cleans and validates the data
3. Saves it to `data/clean/weather_2024_jan.csv`
4. Loads it into a PostgreSQL table called `weather_daily`
5. Answers three business questions with SQL or pandas

See [labs/lab4_weather_pipeline.ipynb](labs/lab4_weather_pipeline.ipynb) for the step-by-step guide.
