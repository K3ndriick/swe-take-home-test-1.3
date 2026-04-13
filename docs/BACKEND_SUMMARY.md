# Backend Summary

## Tech Stack

- **Runtime:** Node.js with TypeScript
- **Framework:** Express 5
- **Module System:** CommonJS (`ts-node-dev` compiles TS `import/export` down to `require()`)
- **Database:** JSON files (no real DB — `vehicles.json` and `checks.json`)
- **Testing:** Jest + Supertest (integration tests)
- **Other:** `uuid` (v9) for generating check IDs, `zod` (installed but unused), `cors`

## Project Structure

```
backend/
├── package.json
├── tsconfig.json
├── jest.config.js
└── src/
    ├── server.ts              # Entry point — starts Express on port 3000
    ├── app.ts                 # Express app setup (cors, json parsing, routes, error handler)
    ├── types.ts               # All TypeScript interfaces
    ├── validation.ts          # Request body validation for POST /checks
    ├── database.ts            # Read/write JSON files (data access layer)
    ├── data/
    │   ├── vehicles.json      # 3 vehicles (VH001, VH002, VH003)
    │   └── checks.json        # Existing inspection records
    ├── controllers/
    │   ├── vehicleController.ts   # GET /vehicles handler
    │   └── checkController.ts     # POST/GET/DELETE /checks handlers
    ├── services/
    │   ├── vehicleService.ts      # getAllVehicles()
    │   └── checkService.ts        # createCheck(), getChecks(), getCheckById(), deleteCheck()
    ├── routes/
    │   ├── index.ts               # Mounts /vehicles and /checks routers
    │   ├── vehicleRoutes.ts       # GET /vehicles
    │   └── checkRoutes.ts         # POST, GET, DELETE /checks
    ├── middleware/
    │   └── errorHandler.ts        # Global error handler (500 responses)
    └── __tests__/
        └── api.test.ts            # Integration tests (supertest)
```

## Request Flow

```
HTTP Request → Routes → Controller → Service → Database (JSON file)
```

## API Endpoints

| Method | Endpoint        | Controller          | Status   |
|--------|-----------------|---------------------|----------|
| GET    | /vehicles       | getAllVehicles       | Working  |
| POST   | /checks         | createCheck         | NOT IMPLEMENTED (returns 501) |
| GET    | /checks         | getChecks           | Working  |
| DELETE | /checks/:id     | deleteCheck         | NOT IMPLEMENTED (returns 501) |

## Data Models (types.ts)

### Vehicle
| Field        | Type   | Example     |
|--------------|--------|-------------|
| id           | string | "VH001"     |
| registration | string | "ABC123"    |
| make         | string | "Toyota"    |
| model        | string | "Hilux"     |
| year         | number | 2022        |

### Check (Inspection Record)
| Field      | Type         | Description                                  |
|------------|--------------|----------------------------------------------|
| id         | string       | UUID, auto-generated                         |
| vehicleId  | string       | References a Vehicle                         |
| odometerKm | number       | Must be > 0                                  |
| items      | CheckItem[]  | Exactly 5 items (TYRES, BRAKES, LIGHTS, OIL, COOLANT) |
| note       | string?      | Optional, max 300 chars                      |
| hasIssue   | boolean      | Computed — true if any item status is "FAIL" |
| createdAt  | string       | ISO 8601 timestamp, auto-generated           |

### CheckItem
| Field  | Type            | Values              |
|--------|-----------------|----------------------|
| key    | CheckItemKey    | "TYRES" \| "BRAKES" \| "LIGHTS" \| "OIL" \| "COOLANT" |
| status | CheckItemStatus | "OK" \| "FAIL"      |

### ErrorResponse
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request",
    "details": [{ "field": "vehicleId", "reason": "is required" }]
  }
}
```

## Validation (validation.ts)

`validateCheckRequest(body)` checks:
- `vehicleId` — required, string, must exist in vehicles.json
- `odometerKm` — required, number, must be > 0
- `items` — required, array of exactly 5, all 5 keys present, no duplicates, valid statuses
- `note` — optional, string, max 300 chars

Returns `ValidationError[]` (empty = valid).

## Tasks to Complete

### Task 1: Implement `createCheck` controller (checkController.ts:29-31)
Currently returns 501. Need to:
1. Validate with `validateCheckRequest(req.body)`
2. Return 400 with `ErrorResponse` if validation fails
3. Call `checkService.createCheck()` if valid
4. Return 201 with created check

### Task 2: Fix `hasIssue` bug (checkService.ts:44-56)
Two bugs:
- **Line 46:** Compares `item.status === "FAILED"` but valid status is `"FAIL"`
- **Line 54:** Sets `hasIssue: !hasIssue` — the negation is inverted

### Task 3: Complete `deleteCheck` (controller + service)
- **Controller (checkController.ts:120-136):** Returns 501. Need to call service and return 204 on success, 404 if not found.
- **Service (checkService.ts:135-137):** Just returns `false`. Need to actually filter out the check and write to DB.

## Testing

- `npm test` runs Jest with coverage
- Tests mock the database module (no real file I/O)
- Two tests are `.skip`-ped — unskip after completing Tasks 1 and 2
- Delete tests are already active — they'll pass once Task 3 is done

## Scripts

| Command          | Description                    |
|------------------|--------------------------------|
| `npm run dev`    | Start dev server (ts-node-dev) |
| `npm start`      | Start production server        |
| `npm run build`  | Compile TypeScript             |
| `npm test`       | Run tests with coverage        |
| `npm run test:watch` | Run tests in watch mode    |
