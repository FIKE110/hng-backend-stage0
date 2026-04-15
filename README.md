# Gender Classification API

A backend API that classifies names by gender using the Genderize.io API.

## Overview

This API exposes an endpoint `/api/classify` that takes a name as a query parameter and returns processed gender prediction data from Genderize.io.

## Endpoints

### GET /api/classify

Classifies a name by gender.

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| name | string | Yes | The name to classify |

**Success Response (200):**
```json
{
  "status": "success",
  "data": {
    "name": "John",
    "gender": "male",
    "probability": 0.99,
    "sample_size": 1234,
    "is_confident": true,
    "processed_at": "2026-04-15T12:00:00Z"
  }
}
```

**Error Responses:**

- **400 Bad Request** - Missing or empty name parameter, or no prediction available
```json
{
  "status": "error",
  "message": "Missing name parameter"
}
```

- **422 Unprocessable Entity** - Non-string name provided
```json
{
  "status": "error",
  "message": "Name must be a string"
}
```

- **500/502** - Server or external API errors
```json
{
  "status": "error",
  "message": "External API error"
}
```

## Processing Rules

1. **Data Extraction**: Extracts `gender`, `probability`, and `count` from Genderize.io response
2. **Field Renaming**: Renames `count` to `sample_size`
3. **Confidence Calculation**: `is_confident` is `true` only when:
   - `probability >= 0.7` AND
   - `sample_size >= 100`
4. **Timestamp**: Generates `processed_at` in UTC ISO 8601 format on every request

## Edge Cases

- If Genderize.io returns `gender: null` or `count: 0`, returns an error response
- Handles external API failures with appropriate status codes (500/502)

## CORS

The API includes the header `Access-Control-Allow-Origin: *` to allow cross-origin requests.

## Tech Stack

- **Hono** - Edge-first web framework
- **TypeScript**
- **Vercel** - Deployment platform

## Local Development

```bash
# Install dependencies
npm install

# Run development server
npx tsx src/index.ts
# or
vc dev
```

The server runs on `http://localhost:3000`

## API Examples

```bash
# Successful classification
curl "http://localhost:3000/api/classify?name=john"

# Missing name parameter
curl "http://localhost:3000/api/classify"

# Empty name
curl "http://localhost:3000/api/classify?name="
```

## Deployment

The API is deployed on Vercel. The endpoint is available at your Vercel deployment URL.

To deploy:
```bash
npm install
vc deploy
```
