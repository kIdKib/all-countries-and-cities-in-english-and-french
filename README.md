# Countries & Cities Database

This directory contains a comprehensive database of countries and their cities, available in both English and French.

## Overview

The `countries.json` file contains **203 countries** with over **1,500 cities** worldwide, providing bilingual support (English/French) for location-based features in the El Rondo application.

## Data Structure

Each country entry follows this format:

```json
{
  "CountryName": {
    "name_en": "Country Name (English)",
    "name_fr": "Nom du Pays (Français)",
    "cities": ["City1", "City2", "City3", ...]
  }
}
```

### Example

```json
{
  "France": {
    "name_en": "France",
    "name_fr": "France",
    "cities": [
      "Paris",
      "Marseille",
      "Lyon",
      "Toulouse",
      "Nice",
      "Nantes",
      "Strasbourg",
      "Montpellier",
      "Bordeaux",
      "Lille"
    ]
  },
  "Spain": {
    "name_en": "Spain",
    "name_fr": "Espagne",
    "cities": [
      "Madrid",
      "Barcelona",
      "Valencia",
      "Seville",
      "Zaragoza",
      "Málaga",
      "Murcia",
      "Palma",
      "Las Palmas",
      "Bilbao"
    ]
  }
}
```

## Usage

### 1. Automatic Database Seeding

The countries data is **automatically loaded into the database** when the server starts for the first time.

**How it works:**
- On server startup, the `seedCountries()` function checks if the `Country` table is empty
- If empty, it reads `countries.json` and populates the database
- Subsequent startups skip seeding since data already exists

**Location:** `utils/seedCountries.js`

```javascript
// Automatically called in index.js
server.listen(port, async () => {
  console.log(`Server listening on ${port}`);

  // Seed countries on startup (only runs if table is empty)
  try {
    await seedCountries();
  } catch (error) {
    console.error('Failed to seed countries, but server is running:', error.message);
  }
});
```

### 2. API Endpoints

Once seeded, you can access country and city data through the REST API:

#### Get All Countries
```bash
GET /api/v1/country
```

**Response:**
```json
{
  "success": true,
  "count": 203,
  "countries": [
    {
      "id": "uuid",
      "name_en": "France",
      "name_fr": "France",
      "cities": ["Paris", "Marseille", "Lyon", ...],
      "createdAt": "2025-12-04T15:38:22.698Z",
      "updatedAt": "2025-12-04T15:38:22.698Z"
    },
    ...
  ]
}
```

#### Get Countries by Language
```bash
GET /api/v1/country/language?lang=fr
```

**Query Parameters:**
- `lang`: Language preference (`en` or `fr`)

**Response:**
```json
{
  "success": true,
  "count": 203,
  "countries": [
    {
      "id": "uuid",
      "name": "France",  // Uses name_fr when lang=fr
      "name_en": "France",
      "name_fr": "France",
      "cities": ["Paris", "Marseille", ...]
    },
    ...
  ]
}
```

#### Search Countries
```bash
GET /api/v1/country/search?query=franc
```

**Query Parameters:**
- `query`: Search term (minimum 2 characters)

**Response:**
```json
{
  "success": true,
  "count": 1,
  "countries": [
    {
      "id": "uuid",
      "name_en": "France",
      "name_fr": "France",
      "cities": ["Paris", "Marseille", ...]
    }
  ]
}
```

#### Get Specific Country
```bash
GET /api/v1/country/:id
```

**Response:**
```json
{
  "id": "uuid",
  "name_en": "France",
  "name_fr": "France",
  "cities": ["Paris", "Marseille", "Lyon", ...],
  "createdAt": "2025-12-04T15:38:22.698Z",
  "updatedAt": "2025-12-04T15:38:22.698Z"
}
```

#### Get Cities for a Country
```bash
GET /api/v1/country/:id/cities
```

**Response:**
```json
{
  "success": true,
  "country": {
    "name_en": "France",
    "name_fr": "France"
  },
  "cities": [
    "Paris",
    "Marseille",
    "Lyon",
    "Toulouse",
    "Nice",
    ...
  ]
}
```

### 3. Direct File Usage

You can also import the JSON file directly in your code:

```javascript
const countries = require('./utils/countries.json');

// Get all country names
const countryNames = Object.keys(countries);

// Get cities for a specific country
const franceCities = countries['France'].cities;

// Get French name for a country
const frenchName = countries['Spain'].name_fr; // "Espagne"
```

### 4. Database Schema

The data is stored in the `Country` model:

```prisma
model Country {
  id        String   @id @default(uuid())
  name_en   String   @unique
  name_fr   String
  cities    Json     // Array of cities stored as JSON
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([name_en])
  @@index([name_fr])
}
```

**Indexes:**
- Indexed on `name_en` for fast English name lookups
- Indexed on `name_fr` for fast French name lookups

### 5. Using with Prisma

Query countries directly from the database:

```javascript
const { PrismaClient } = require('../generated/prisma');
const prisma = new PrismaClient();

// Get all countries
const countries = await prisma.country.findMany();

// Find a country by English name
const france = await prisma.country.findUnique({
  where: { name_en: 'France' }
});

// Search countries (case-insensitive)
const results = await prisma.country.findMany({
  where: {
    OR: [
      { name_en: { contains: 'united', mode: 'insensitive' } },
      { name_fr: { contains: 'united', mode: 'insensitive' } }
    ]
  }
});
```

## Coverage

The database includes:
- **54 African countries** (Algeria, Egypt, Nigeria, South Africa, etc.)
- **51 European countries** (France, Germany, Spain, United Kingdom, etc.)
- **50 Asian countries** (China, India, Japan, South Korea, etc.)
- **35 American countries** (United States, Canada, Brazil, Argentina, etc.)
- **13 Oceanian countries** (Australia, New Zealand, Fiji, etc.)

## Use Cases

1. **Player Profiles**: Set player location and nationality
2. **Recruiter/Organization Profiles**: Set base location and operating regions
3. **Trial Events**: Set trial location by country and city
4. **Search & Filters**: Filter players/recruiters by location
5. **Auto-complete Forms**: Provide dropdown menus for country and city selection
6. **Internationalization**: Display location names in user's preferred language
7. **Statistics**: Generate geographic statistics (players per country, etc.)

## Maintenance Scripts

Several Python scripts are available for maintaining the data:

- `add_french_names_fixed.py`: Add French translations to countries
- `add_african_countries.py`: Add missing African countries
- `add_missing_countries.py`: Add missing European/Asian countries
- `add_americas.py`: Add missing American countries
- `check_countries.py`: Verify European/Asian country coverage
- `check_americas.py`: Verify American country coverage

## Benefits

✅ **Comprehensive Coverage**: 203 countries, 1500+ cities worldwide
✅ **Bilingual Support**: English and French names for all countries
✅ **Automatic Seeding**: No manual database setup required
✅ **Fast Queries**: Indexed for optimal search performance
✅ **RESTful API**: Easy integration with frontend/mobile apps
✅ **Type-Safe**: Prisma schema ensures data consistency
✅ **Swagger Documentation**: Auto-generated API documentation

## Adding New Countries or Cities

To add new countries or cities:

1. Edit `utils/countries.json` following the existing format
2. Restart the server or manually run the seed script:
   ```bash
   node -e "require('./utils/seedCountries').seedCountries()"
   ```
3. The database will be updated with new entries

## Notes

- The seeding process is **idempotent** - it only runs if the table is empty
- Cities are stored as JSON arrays for flexible querying
- All country names (English) are **unique** in the database
- The API supports case-insensitive search in both languages
