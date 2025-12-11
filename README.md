# Countries & Cities Database

This directory contains a comprehensive database of countries and their cities, available in both English and French.

## Overview

The `countries.json` file contains **203 countries** with over **1,500 cities** worldwide, providing bilingual support (English/French) for location-based.

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

