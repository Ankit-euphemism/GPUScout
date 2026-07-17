# GPUScout

GPUScout is a small Flask web app for exploring graphics-card listings priced at or below ₹20,000. Search listings by keyword, set a maximum price, and open the retailer's product page from a responsive interface.

![GPUScout interface](image/image.png)

## Features

- Keyword search across listing titles and descriptions
- Adjustable maximum-price filter (₹5,000–₹20,000)
- Responsive Bootstrap-based interface
- JSON API for all listings and filtered search results
- Local JSON-backed dataset—no database required

> Listing details and prices are sample data and may no longer match the retailer's current information. Always verify a price and availability on the linked product page.

## Stack

- Python 3 and Flask
- Jinja2 templates, Bootstrap 5, CSS, and vanilla JavaScript
- JSON files for listing data
- Gunicorn for production deployment

## Quick start

Clone the repository and enter the project directory:

```bash
git clone https://github.com/Ankit-euphemism/Simple-GPU-Finder.git
cd Simple-GPU-Finder
```

Create and activate a virtual environment:

```bash
# Windows (PowerShell)
python -m venv .venv
.venv\Scripts\Activate.ps1

# macOS / Linux
python3 -m venv .venv
source .venv/bin/activate
```

Install the dependencies and start the app:

```bash
pip install -r requirements.txt
python app.py
```

Open <http://localhost:8000> in a browser.

### Environment variables

Copy the example file before deploying or running outside local development:

```bash
# Windows (PowerShell)
copy .env.example .env

# macOS / Linux
cp .env.example .env
```

`SECRET_KEY` should be set to a long random value in production. Set `FLASK_DEBUG=1` only for local development; the application is otherwise started without Flask debug mode.

## Data

The app reads its listings from `data/graphic_cards.json`. Each item uses the following source-shaped fields:

```json
{
  "title_elem": "Example graphics card",
  "price_elem": "₹14,999.00",
  "link_elem": "https://example.com/product"
}
```

At request time, `app.py` turns those fields into `title`, numeric `price`, `link`, `source`, and `description` values for the page and API responses.

`data/scraping.json` holds the raw sample input. `scraper.py` is a work-in-progress data-preparation script; the running web app does not call it, and the **Refresh Data** button currently reports the number of already-loaded records before reloading the page. Update `data/graphic_cards.json` directly when you need to change the listings.

## API

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/` | Renders the GPU listing page. |
| `GET` | `/api/products` | Returns all listings as JSON. |
| `GET` | `/search?q=<query>&max_price=<amount>` | Returns listings matching the keyword and price ceiling. |
| `POST` | `/refresh` | Returns a refresh-status response; it does not scrape new data. |

For example:

```text
GET /search?q=rtx&max_price=15000
```

The search response is an array like:

```json
[
  {
    "title": "Example graphics card",
    "description": "",
    "price": 14999,
    "source": "N/A",
    "link": "https://example.com/product"
  }
]
```

## Project layout

```text
Simple-GPU-Finder/
├── app.py                 # Flask routes and data transformation
├── scraper.py             # Experimental data-preparation script
├── data/
│   ├── graphic_cards.json # Listings served by the app
│   └── scraping.json      # Raw sample listing data
├── static/
│   ├── css/style.css      # Application styles
│   └── js/app.js          # Search, filtering, and UI interactions
├── templates/             # Jinja templates
├── Procfile               # Gunicorn process definition
└── render.yml             # Render deployment configuration
```

## Deployment

The repository includes `Procfile` and `render.yml` for a Render web service. Configure a production `SECRET_KEY` in Render and deploy using the included build and start commands. The app binds to the platform-provided `PORT` through Gunicorn.
