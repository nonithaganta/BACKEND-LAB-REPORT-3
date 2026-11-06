# Lab 3: REST API, query parameters, validation, debugging

**Name:** G.Nonitha  
**Registration number:** 252U1R6073
**Section / Class:** 207-AIML-2B  
**Course:** Backend Development Fundamentals · Year II Term II

## Live link
Not deployed. Run locally using the instructions below. Replace this line with the public URL if you deploy the server.

## Objective
Upgrade the Lab 2 Express and SQLite application into a REST API with search, sorting, pagination, price filtering, validation, correct status codes, and debugging practice.

## Project structure
- `src/server.js` — Express server and REST API routes.
- `database/books.db` — SQLite database with 10 sample books.
- `database/schema.sql` — table definition and sample data.
- `lab03.postman_collection.json` — Postman success and failure test requests.
- `.env.example` — example configuration.
- `screenshots/` — add genuine screenshots from your own tests.

## Requirements
Node.js, npm, Postman, and optionally SQLite CLI.

## Install and run
1. Open this `lab-03` folder in VS Code.
2. Open the terminal in this folder.
3. Run `npm install`.
4. Optional: copy `.env.example` to `.env`. Defaults are port `3000` and `database/books.db`. Supported variables: `PORT` and `DATABASE_PATH`. Do not commit `.env`.
5. Run `npm run dev` (nodemon) or `npm start`.
6. Open `http://localhost:3000/books`.

## API endpoints

| Method | Path | Description | Success status |
|---|---|---|---|
| GET | `/books` | Get books with optional search, sort, pagination and price filters. | 200 |
| GET | `/books/:bookId` | Get one book by ID. | 200 |
| POST | `/books` | Create a validated book. | 201 |
| PUT | `/books/:bookId` | Update an existing book. | 200 |
| DELETE | `/books/:bookId` | Delete an existing book. | 200 |

## GET /books query parameters

| Parameter | Default | Description |
|---|---|---|
| `search_q` | empty | Partial title search using SQL `LIKE`. |
| `order_by` | `id` | Allowed columns: `id`, `title`, `author`, `price`, `pages`. |
| `order` | `ASC` | Sort direction: `ASC` or `DESC`. |
| `limit` | `10` | Positive integer maximum rows returned. |
| `offset` | `0` | Non-negative integer number of rows skipped. |
| `min_price` | not applied | Inclusive minimum price. |
| `max_price` | not applied | Inclusive maximum price. |

Examples:
- `GET /books`
- `GET /books?search_q=atomic`
- `GET /books?order_by=price&order=DESC`
- `GET /books?limit=5&offset=0`
- `GET /books?min_price=200&max_price=500`
- `GET /books?search_q=the&order_by=title&order=ASC&limit=5&offset=0&min_price=200&max_price=500`

Sort column and direction are allow-listed. User values use SQL placeholders and parameter arrays.

## Example JSON body

POST `/books`:
```json
{
  "title": "Python Basics",
  "author": "John Smith",
  "price": 350,
  "pages": 250
}
```

PUT `/books/1`:
```json
{
  "title": "The Alchemist Updated",
  "author": "Paulo Coelho",
  "price": 400,
  "pages": 210
}
```

Title and author must be non-empty strings, price a non-negative number, and pages a positive integer. Invalid bodies return `400` with a JSON message.

## HTTP status codes
- `200 OK`: successful GET, PUT, or DELETE.
- `201 Created`: successful book creation.
- `400 Bad Request`: invalid ID, query parameter, or request body.
- `404 Not Found`: requested book or route does not exist.
- `500 Internal Server Error`: unexpected database/server failure.

Error responses use a consistent JSON `message` property.

## Postman testing
Import `lab03.postman_collection.json` and set `baseUrl` to `http://localhost:3000`. It contains tests for:
- Get all books; search, sort, pagination, and price range.
- Invalid sort and price range.
- Existing, missing, and invalid book IDs.
- Valid and invalid POST bodies.
- Existing and missing PUT/DELETE records.

For POST and PUT, choose Body → raw → JSON.

## Debugging exercise

| Error | Cause | Solution |
|---|---|---|
| `EADDRINUSE` | Another process uses the port. | Stop the other server or set a free `PORT`. |
| Wrong import path | Incorrect module/file path. | Check spelling and relative path. |
| `no such table` | SQL table name does not match the database. | Check `.tables` and use the correct table name. |
| Missing `await` | Async database operation is not awaited. | Use `await` inside an `async` function and handle errors. |

## Inspect SQLite
Run `sqlite3 database/books.db`, then:
```sql
.tables
SELECT * FROM book;
SELECT COUNT(*) FROM book;
.quit
```

## Screenshots to add
Capture real screenshots from your Postman and SQLite runs and save them in `screenshots/`:
- `get-books.png`
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c96d1346-aa33-40a9-8bee-486460154506" />

- `search-sort-filter.png`
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cfc33669-0c59-4ea7-8261-841b312bf6f3" />

- `get-book-by-id.png`
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bee199b3-cb9e-4d2d-a986-643aff0f4dcd" />

- `post-valid.png`
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/435c0df7-f6d1-4034-bf65-32411cc176db" />

- `post-invalid.png`
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7250dcc1-7951-4de2-9e9c-26ffbbc10c19" />

- `put-book.png`
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a510a553-69b6-45d1-aab4-e6f28913bd37" />

- `delete-book.png`
- <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/63d97de3-2a75-41b4-820a-94eba9953b84" />

- `not-found-and-invalid-query.png`
- 
- `sqlite-database.png`

Embed each screenshot in this README, for example:
`![GET books response](screenshots/get-books.png)`

Screenshots are not fabricated or included as if testing has already occurred. Add your actual results before submission.

## Notes: problems faced and solutions
- Search and price filters are built from conditional SQL clauses with parameter arrays.
- Sorting uses an allow-list because SQL identifiers cannot be supplied as ordinary `?` parameters.
- Input validation checks query parameters, IDs, and book fields.
- Database operations use `await` and `try/catch`.
- `.gitignore` excludes `node_modules/` and `.env`.

## Viva preparation

**1. Query parameter vs path parameter?**  
A query parameter appears after `?` and is used for search, filtering, sorting, or pagination. A path parameter is part of the URL path and identifies a resource, such as `/books/5`.

**2. What does stateless mean?**  
A stateless API does not store client session state between requests. Each request contains the information needed to process it.

**3. Which status code for bad input vs a missing record?**  
Use `400 Bad Request` for invalid input and `404 Not Found` when a requested record does not exist.
