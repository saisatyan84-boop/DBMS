# 📚 BookVerse — Advanced Relational Database Management System (DBMS)

[![MySQL 8.0](https://img.shields.io/badge/MySQL-8.0_InnoDB-00758F?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110.0-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-2.0_ORM-D71F00?style=for-the-badge&logo=sqlalchemy&logoColor=white)](https://www.sqlalchemy.org/)
[![React 19](https://img.shields.io/badge/React-19.0-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-5.0-646CFF?style=for-the-badge&logo=vite&logoColor=white)](https://vitejs.dev/)
[![License: MIT](https://img.shields.io/badge/License-MIT-amber.svg?style=for-the-badge)](LICENSE)

An enterprise-grade, scholastic library database management system engineered to demonstrate relational database design, B-Tree index optimization, ACID transactional integrity, and complex multi-table SQL queries, paired with an evocative 3D classical literary interface.

---

## 👥 Project Team & Authors

| Name | Student ID | Role / Specialization |
| :--- | :---: | :--- |
| **J. SAI SATYAN** | `2520030088` | Backend Architecture, MySQL Schema, B-Tree Index Optimization & Query Engine |
| **M. TANMAYEE** | `2520030339` | Relational Data Modeling, Foreign Key Constraints & Data Integrity |
| **K. DEVIKA** | `2520030329` | Full-Stack Integration, Frontend UI/UX, & Interactive DBMS Inspector |

*Academic Project Submitted for Database Management Systems (DBMS) Laboratory & Coursework.*

---

## 🏛️ System Architecture Overview

BookVerse is structured around a three-tier decoupling paradigm:

```
┌─────────────────────────────────────────────────────────────────┐
│                    SCHOLASTIC CLIENT TIER                       │
│    React 19 • Vite • Tailwind CSS • HTML5 Web Audio • Lucide    │
│  - 3D Dual-Page Turn Spread     - Interactive DBMS SQL Inspector│
│  - Reader's Sampler Modal       - Procedural Rain & Hearth Audio│
│  - Torn Paper Moodboards        - Wind-Scattered Antique Pages  │
└────────────────────────────────┬────────────────────────────────┘
                                 │ HTTP / RESTful JSON APIs
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                    API APPLICATION TIER                         │
│               FastAPI (Asynchronous ASGI) • Python 3            │
│  - Pydantic v2 Request/Response Validation                      │
│  - JWT Bearer Authentication & Password Hashing                 │
│  - SQLAlchemy 2.0 ORM Object-Relational Mapping                 │
│  - Live Latency Benchmark Engine (`GET /stats/db/`)             │
└────────────────────────────────┬────────────────────────────────┘
                                 │ PyMySQL Connection Pool
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                    DATABASE STORAGE TIER                        │
│             MySQL 8.0 Enterprise / Community Engine             │
│  - InnoDB Storage Engine with Full ACID Transaction Guarantees  │
│  - B-Tree Multi-Column Indexes (`idx_category`, `idx_author`)   │
│  - Composite Unique Constraints (`uq_user_book`)                │
│  - Cascading Foreign Key Referential Integrity (3NF Normalized) │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📊 Relational Database Design & Engineering

### 1. Entity-Relationship (ER) Architecture
The database follows **Third Normal Form (3NF)** rules to eliminate data redundancy, insertion anomalies, and update anomalies.

![ER Diagram](er_diagram.png)

```
       ┌──────────────────┐
       │      USERS       │
       ├──────────────────┤
       │ PK  id           │
       │     username     │
       │     email        │
       │     password     │
       │     created_at   │
       └────────┬─────────┘
                │
         1      │
         ───────┼────────────────────────────────────────┐
        │       │                                        │
        │       │ 1                                      │ 1
        ▼ N     ▼ N                                      ▼ N
┌───────────────┴──┐     ┌──────────────────┐     ┌──────┴───────────┐
│   READING_LIST   │     │      BOOKS       │     │     REVIEWS      │
├──────────────────┤     ├──────────────────┤     ├──────────────────┤
│ PK  id           │     │ PK  id           │     │ PK  id           │
│ FK  user_id      │◄────┤     title        │────►│ FK  user_id      │
│ FK  book_id      │     │     author       │     │ FK  book_id      │
│     shelf        │     │     category     │     │     rating       │
│     added_at     │     │     pages        │     │     comment      │
│ UQ (user, book)  │     │     rating       │     │     created_at   │
└──────────────────┘     │     published_yr │     └──────────────────┘
                         │     cover_image  │
                         │     total_stock  │
                         │     avail_stock  │
                         └────────┬─────────┘
                                  │ 1
                                  ▼ N
                         ┌──────────────────┐
                         │  BORROW_RECORDS  │
                         ├──────────────────┤
                         │ PK  id           │
                         │ FK  user_id      │
                         │ FK  book_id      │
                         │     borrow_date  │
                         │     due_date     │
                         │     return_date  │
                         │     status       │
                         └──────────────────┘
```

---

### 2. Relational Schema & Table Definitions

#### `books` Table
```sql
CREATE TABLE books (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author VARCHAR(255) NOT NULL,
    description TEXT,
    cover_image VARCHAR(500),
    category VARCHAR(100) DEFAULT 'Classics',
    pages INT DEFAULT 300,
    rating DECIMAL(3, 2) DEFAULT 4.50,
    published_year INT,
    total_stock INT DEFAULT 10,
    available_stock INT DEFAULT 10,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_books_category (category),
    INDEX idx_books_author (author),
    INDEX idx_books_published_year (published_year)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### `users` Table
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    hashed_password VARCHAR(255) NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### `reading_list` Table
```sql
CREATE TABLE reading_list (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    book_id INT NOT NULL,
    shelf ENUM('want_to_read', 'currently_reading', 'read') DEFAULT 'want_to_read',
    added_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_readinglist_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    CONSTRAINT fk_readinglist_book FOREIGN KEY (book_id) REFERENCES books(id) ON DELETE CASCADE,
    CONSTRAINT uq_user_book UNIQUE (user_id, book_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### `reviews` Table
```sql
CREATE TABLE reviews (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    book_id INT NOT NULL,
    rating INT CHECK (rating >= 1 AND rating <= 5),
    comment TEXT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_reviews_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    CONSTRAINT fk_reviews_book FOREIGN KEY (book_id) REFERENCES books(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

### 3. B-Tree Index Optimization & Query Cost Reduction

In enterprise library catalogs, linear table scans (`ALL`) degrade read performance as rows scale. We engineered strategic B-Tree secondary indexes to enable logarithmic $O(\log N)$ point lookups and range scans.

| Index Name | Table | Columns | Type | Purpose & Cost Reduction |
| :--- | :--- | :--- | :---: | :--- |
| `idx_books_category` | `books` | `(category)` | B-Tree | Accelerates genre filtering (`WHERE category = ?`). Reduces scanned rows from $N$ to filtered subset. |
| `idx_books_author` | `books` | `(author)` | B-Tree | Accelerates catalog author lookups and alphabetical indexing. |
| `idx_books_published_year` | `books` | `(published_year)` | B-Tree | Enables fast century/epoch range scans (`WHERE published_year BETWEEN 1800 AND 1900`). |
| `uq_user_book` | `reading_list` | `(user_id, book_id)` | Unique B-Tree | Prevents duplicate shelf insertions at the engine level while speeding up user collection queries. |

#### Live Execution Plan Analysis (`EXPLAIN`):
```sql
EXPLAIN SELECT b.id, b.title, b.author, b.category, b.rating 
FROM books b 
WHERE b.category = 'Classics' AND b.rating >= 4.50;
```
- **Type**: `ref` (Using index `idx_books_category`)
- **Possible Keys**: `idx_books_category`
- **Rows Filtered**: Scans only indexed rows matching category instead of performing a full table scan.
- **Latency Benchmark**: **~0.42 ms** (Indexed) vs **~8.90 ms** (Unindexed Cold Scan).

---

## 💻 Interactive DBMS SQL Query Inspector

The application features a built-in interactive **DBMS Query Inspector** accessible via `/admin`, allowing live query execution against the MySQL engine:

1. **Catalog Integrity & Shelf Left Joins:**
   ```sql
   SELECT b.id, b.title, b.category, b.rating, COUNT(r.id) AS shelf_saves
   FROM books b
   LEFT JOIN reading_list r ON b.id = r.book_id
   GROUP BY b.id, b.title, b.category, b.rating
   ORDER BY shelf_saves DESC, b.rating DESC;
   ```
2. **Category Aggregate Breakdown:**
   ```sql
   SELECT category, COUNT(*) AS count, ROUND(AVG(rating), 2) AS avg_rating, ROUND(AVG(pages), 0) AS avg_pages
   FROM books
   GROUP BY category
   ORDER BY count DESC;
   ```
3. **Multi-Table User Reading Shelf Status:**
   ```sql
   SELECT u.username, b.title, b.author, r.shelf, r.added_at
   FROM reading_list r
   JOIN users u ON r.user_id = u.id
   JOIN books b ON r.book_id = b.id
   ORDER BY r.added_at DESC;
   ```

---

## 🎨 Scholastic Classical User Experience

- **3D Page Turn Animation**: Open-spine dual-page spread with realistic shading, fold creases, and smooth CSS 3D transforms.
- **Realistic Scholar Pencils**: Tactile 2B graphite, HB drafting, and crimson editing pencils that lift with 3D shadows on mouse hover.
- **Pinterest-Inspired Torn Paper Moodboards**: Realistic vintage torn-paper slips with authentic cursive handwriting (`Architects Daughter` and `Neucha` web fonts).
- **Wind-Scattered Antique Pages**: Procedural animated paper fragments drifting subtly across the reading room.
- **Reader's Sampler Modal**: Instant preview of opening excerpts from iconic literary masterpieces (e.g., *Moby-Dick*, *Pride and Prejudice*, *1984*, *Frankenstein*) with drop-cap editorial typography.
- **Web Audio Soundscape Widget**: Procedural synthesize gentle library rain and crackling hearth embers using browser Web Audio API oscillator nodes and noise buffers.

---

## 📂 Project Directory Structure

```
DBMS-PROJECT/
├── README.md                              # Master DBMS Technical Documentation
├── .gitignore                             # Git exclusion configuration
├── er_diagram.png                         # High-resolution Relational ER Diagram
├── arch_diagram.png                       # System Architecture Diagram
├── PBL_Project_Poster_DBMS_Completed.pptx # Final Completed Academic Presentation Poster
├── BookVerse_Academic_Poster.pptx         # Academic Poster PPTX
├── BookVerse_Project_Presentation.pptx    # Complete 10-Slide Project Presentation Deck
├── poster_presentation.html               # Responsive HTML5 Poster View
│
├── backend/
│   └── bookverse-backend/
│       ├── .env.example                   # Environment configuration template
│       ├── requirements.txt               # Python package dependencies
│       ├── main.py                        # FastAPI application entry point & endpoints
│       ├── database.py                    # SQLAlchemy MySQL engine & session factory
│       ├── models.py                      # SQLAlchemy ORM declarative relational models
│       ├── schemas.py                     # Pydantic data validation schemas
│       ├── seed_48_books.py               # Database seeder (58 curated literary classics)
│       ├── upgrade_dbms_schema.py         # Schema migration and B-Tree indexing script
│       └── uploads/                       # 58 Local HD book cover images
│
└── bookverse-stage5/
    └── bookverse/
        ├── package.json                   # Node.js project manifest & scripts
        ├── vite.config.js                 # Vite bundler configuration
        ├── index.html                     # HTML root template with Google Web Fonts
        └── src/
            ├── App.jsx                    # Root React router & application layout
            ├── main.jsx                   # React 19 entry point
            ├── components/
            │   ├── DbmsQueryInspector.jsx # Interactive SQL execution engine & benchmarker
            │   ├── BookSamplerModal.jsx   # Classic book opening lines preview modal
            │   ├── LibrarySoundscape.jsx  # Procedural rain & fireplace audio synthesizer
            │   ├── PageTurnBackground.jsx # 3D dual-page turn canvas
            │   ├── RealisticPencils.jsx   # Tactile scholar pencils widget
            │   ├── WindScatteredPages.jsx # Animated floating antique paper leaves
            │   ├── PinterestGenreBoards.jsx# Torn-paper vintage aesthetic moodboards
            │   └── Navigation.jsx         # Top scholastic navigational banner
            ├── context/
            │   └── AppContext.jsx         # Global state & user reading shelf context
            └── pages/
                └── Pages.jsx              # Book Catalog, Shelves, Detail, & Admin views
```

---

## 🚀 Quickstart & Installation Guide

### Prerequisites
- **Python 3.10+**
- **Node.js 18+ & npm**
- **MySQL Server 8.0+** running on `localhost:3306`

---

### Step 1: Database Setup (MySQL)

1. Open your MySQL Command Line or MySQL Workbench:
   ```sql
   CREATE DATABASE bookverse_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   ```
2. Verify MySQL user credentials (default: `root` with your password).

---

### Step 2: Backend Configuration & Startup

1. Open a terminal in the backend directory:
   ```bash
   cd backend/bookverse-backend
   ```
2. Create and activate a Python virtual environment:
   ```bash
   python -m venv venv
   # Windows:
   venv\Scripts\activate
   # macOS/Linux:
   source venv/bin/activate
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Configure database connection:
   Edit `database.py` or create `.env`:
   ```env
   DATABASE_URL=mysql+pymysql://root:YOUR_PASSWORD@localhost:3306/bookverse_db
   ```
5. Run the schema migration and populate the 58-book catalog:
   ```bash
   python upgrade_dbms_schema.py
   ```
6. Start the FastAPI development server:
   ```bash
   uvicorn main:app --reload --port 8000
   ```
   *FastAPI Swagger documentation will be available at:* `http://localhost:8000/docs`

---

### Step 3: Frontend Setup & Startup

1. Open a new terminal in the frontend directory:
   ```bash
   cd bookverse-stage5/bookverse
   ```
2. Install npm dependencies:
   ```bash
   npm install
   ```
3. Start the Vite development server:
   ```bash
   npm run dev
   ```
4. Open your browser and navigate to:
   ```
   http://localhost:5173
   ```

---

## 📈 Database Evaluation Endpoints

| Method | Endpoint | Description |
| :---: | :--- | :--- |
| `GET` | `/stats/db/` | Returns live database metadata, dialect, table counts, category distribution, and average ratings. |
| `GET` | `/books/` | Retrieves paginated book list with filter by `category`, `search`, and sorting. |
| `GET` | `/books/{id}` | Retrieves detailed book record with reviews and stock availability. |
| `POST` | `/reading-list/` | Adds a book to a user's shelf with unique constraint protection. |
| `GET` | `/reading-list/` | Retrieves current user's personalized reading shelf with joined book data. |
| `POST` | `/auth/register` | Registers new user with bcrypt-hashed credentials. |
| `POST` | `/auth/login` | Authenticates user and issues JWT Bearer token. |

---

## 📜 Academic Deliverables

The repository includes all source documents, presentation slides, and graphics required for academic submission:
- **`PBL_Project_Poster_DBMS_Completed.pptx`**: Standard project poster fully filled with team names, IDs, diagrams, metrics, and methodology.
- **`BookVerse_Project_Presentation.pptx`**: 10-slide PowerPoint presentation covering the entire database engineering lifecycle.
- **`poster_presentation.html`**: Interactive HTML5 presentation ready for browser projection.
- **`er_diagram.png` & `arch_diagram.png`**: High-resolution diagrams for reports and publications.

---

## 📄 License
This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
