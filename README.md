# CS 340 — Grazioso Salvare Animal Shelter Dashboard

**Category:** Databases
**Original course:** CS 340 — Advanced Programming Concepts

## What the original artifact is

A Jupyter/Dash dashboard for the Grazioso Salvare animal rescue scenario. Talks to a MongoDB collection of about 10,000 Austin Animal Center records through a simple Python CRUD module (`animal_shelter.py`) using PyMongo. The dashboard lets you filter animals by rescue type (water, mountain, disaster), see a pie chart of the breed distribution, and view locations on a map. Runs inside Jupyter — which is fine for a class submission, but nobody ships production software that way.

## Why I chose it

The original had two big weaknesses: it ran in Jupyter (not a real web app), and it had zero security — no auth, no input validation, anyone who could reach the notebook could do anything to the database. For the databases category, I wanted to actually turn this into a secure, full-stack application that could plausibly exist in the real world.

## What I enhanced

Rebuilt the backend as a **FastAPI REST service** and swapped the frontend for a standalone HTML/JS page. Added real security and real database features.

- **FastAPI backend** with RESTful endpoints for CRUD plus three aggregation endpoints (breed stats, outcome trends over time, age distribution via `$bucket`)
- **Authentication:** JWT tokens with bcrypt-hashed passwords
- **Role-based access control:** three roles (`admin`, `write`, `read`) — admins can delete, write roles can create/update, read-only can only query
- **Pydantic models** for every request and response — type-checked input validation, automatic OpenAPI docs
- **NoSQL injection sanitization** — MongoDB operators stripped from any incoming query dict
- **Compound indexes** on the fields the dashboard actually queries (`animal_type`, `breed`, `outcome_type`, `date_of_birth`) so the aggregation pipelines aren't doing collection scans
- **MongoDB aggregation pipelines** for the three analytics endpoints, replacing what used to be Pandas operations in the notebook
- **Standalone HTML/JS frontend** using Plotly for charts and Leaflet for the map — no more Jupyter dependency

## Course outcomes this hits

- **Outcome 3 (algorithmic principles, trade-offs):** moving aggregation work from Python/Pandas into MongoDB's aggregation framework pushes the heavy lifting to the database (where it belongs), and compound indexes turn some queries from O(n) collection scans into O(log n) index lookups
- **Outcome 4 (well-founded, innovative tools):** FastAPI, Pydantic, JWT, bcrypt, Leaflet, Plotly — the stack is all current and all industry-standard
- **Outcome 5 (security mindset):** authentication, hashed passwords, role-based access, input validation, and NoSQL injection prevention — security built in at every layer, not bolted on

## Files

- `README.md` — this narrative
- `CS499_Milestone_Four_Daniel_Collins.zip` — full milestone submission with enhanced backend (`app.py`, `animal_shelter.py`, `auth.py`, `models.py`, `config.py`), plus an `original/` folder with the unmodified CS 340 dashboard
- `CS340Project_Two_Submission.zip` — original CS 340 submission for reference

## How to run it

Inside the enhanced folder:

```bash
pip install -r requirements.txt
uvicorn app:app --reload
```

Then hit `http://localhost:8000/docs` for the auto-generated OpenAPI/Swagger UI. MongoDB needs to be running locally on the default port with the `aac` database and `animals` collection populated (CSV import via Compass works).

---

Part of my CS 499 ePortfolio — [dcollins123.github.io](https://dcollins123.github.io)
