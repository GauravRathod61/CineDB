# 🎬 CineDB — Film Industry Database

**CineDB** is a full-scale relational database that models the entire ecosystem of the film industry — from film production and talent management, through distribution, legal compliance, music rights, and box-office/viewership analytics.

It's not just a movie catalogue. CineDB is designed to answer the kind of real, cross-domain business questions that production houses, distributors, legal teams, music labels, and analysts actually ask — things like *"Which production house had the best ROI over the last 5 years?"* or *"Which films have active legal disputes freezing their box office collections?"*

This repository contains the complete database design and implementation: the ER diagram, the relational schema, the DDL scripts, and the queries used to explore the data.

---

## 📂 Project Files

This repository includes the following project artifacts:

- 📄 [ER Diagram](./ER_Diagram.pdf)
- 📄 [Relational Schema](./Relational_Schema.pdf)
- 🛠️ [DDL Script](./DDL_Script.sql)
- 📥 [Sample Data](./Sample_Data.sql)
- 📘 [Query Solutions](./Query_Solutions.pdf)

## 📌 Table of Contents

- Overview
- Why This Project
- Database Scope
- Tech Stack
- Entity-Relationship Design
- Relational Schema
- Core Entities
- How Everything Connects
- Business Questions This DB Answers
- Setup & Installation
- Sample Queries
- Future Scope
- License
---

## 🎯 Overview

CineDB models the film industry as a living commercial and creative ecosystem, covering:

- 🎥 **Film production lifecycle** — from project greenlight to release
- 🧑‍🎤 **Talent & crew management** — actors, directors, composers, lyricists
- 💰 **Financials** — production budgets, contracts, box-office collections
- 🎵 **Music & rights** — songs, albums, royalties, music labels
- 🎦 **Distribution & exhibition** — theatres, distributors, show scheduling
- ⚖️ **Legal & compliance** — censor certification, copyright/contract disputes
- 🏆 **Awards & recognition** — nominations and wins across award bodies
- 📊 **Analytics** — viewership data, genre trends, franchise performance

The database is built on **PostgreSQL** and is fully normalized (up to 3NF/BCNF in most tables), with weak entities, composite keys, and enforced referential integrity throughout. The design is intentionally industry-agnostic — it can represent films, production houses, and certification bodies from any country or market, not tied to a single film industry.

---

## 💡 Why This Project

Most student/portfolio databases model something small and self-contained — a library, a hospital, an e-commerce store. CineDB deliberately tackles a **messier, more realistic domain**: an industry where a single film touches production financing, multi-party contracts, censorship/certification law, music royalties, theatrical *and* OTT distribution, and awards — all at once.

The goal was to design a schema that could genuinely support decision-making for six different types of industry stakeholders (see below), not just store data.

---

## 📂 Database Scope

### ✅ In Scope
| Area | Coverage |
|---|---|
| Production | Film records, budgets, production houses, franchises |
| Talent | Actors, directors, crew, contracts, roles |
| Genre system | Weighted, multi-genre composition per film |
| Music | Songs, albums, labels |
| Distribution | Theatres, distributors, territorial rights, show scheduling |
| Legal | Certification boards, legal disputes |
| Awards | Nominations & wins across award bodies |
| Analytics | Box office, day-wise collections, viewership |

### 🔭 Explicitly Out of Scope (for now)
- A full ticket-booking / POS system
- User authentication & role-based access control
- Film production scheduling (shooting schedules, call sheets)
- Real-time social media API integration
- Payroll processing and accounting
- TV serial or web series production

---

## 🛠 Tech Stack

- **Database Engine:** PostgreSQL
- **Design Tools:** ER diagram + Relational schema diagram (included in this repo)
- **Language:** SQL (DDL + DML + analytical queries)

---



## 🧩 Entity-Relationship Design

The ER diagram captures **27+ entities** connected through both simple and weighted many-to-many relationships. Some key design decisions:

- **`MOVIE`** is the central hub entity — nearly every other entity relates back to it directly or transitively.
- **`GENRE`** is a *weighted* relationship (`weight` attribute) — a film isn't just "Action," it can be 45% Horror / 30% Comedy / 25% Thriller, enabling genre-portfolio analytics.
- **`BOX_OFFICE`** is a strict 1:1 with `MOVIE`, while **`DAY_ENTRY`** is a *weak entity* dependent on `BOX_OFFICE` for daily collection tracking.
- **`CAST_CREW`** is a ternary relationship (`MOVIE` × `PERSON` × `ROLE`) — the same person can appear on the same film in multiple roles (e.g., actor-director).
- **`AWARD_NOMINATION`** is a 5-way composite relationship (`AWARD` × `AWARD_CATEGORY` × `PERSON` × `MOVIE` × `SONG`) to support both person-level and song-level nominations under one structure.
- **`LEGAL_DISPUTE`** bridges `MOVIE` and `CONTRACT`, tying legal/compliance data directly back to financial agreements.
- **`CENSOR_CERTIFICATE`**, **`DISTRIBUTION_RIGHT`**, **`SHOW_SCHEDULE`**, and **`VIEWERSHIP_ANALYTICS`** are all associative entities with composite primary keys, modeling time-bound, many-to-many business relationships (a film can have multiple distributors across multiple territories, multiple shows across multiple theatres, etc.)

- ## 🧩 Entity-Relationship Design

For the complete ER diagram, refer to **[ER_Diagram.pdf](./ER_Diagram.pdf)**.


---

## 🗄 Relational Schema

The logical schema translates the ER model into **27 normalized tables**, organized into five functional groups:

| Group | Tables |
|---|---|
| **Master / Lookup** | `PRODUCTION_HOUSE`, `FRANCHISE`, `ROLE`, `PERSON`, `MUSIC_LABEL`, `AWARD`, `AWARD_CATEGORY`, `GUIDE_CATEGORY`, `THEATRE`, `CENSOR_BOARD`, `DISTRIBUTOR` |
| **Core** | `MOVIE` |
| **Talent / Music / Legal** | `CAST_CREW`, `ALBUM`, `SONG`, `CONTRACT`, `LEGAL_DISPUTE`, `REVIEW`, `GENRE` |
| **Awards & Guidance** | `AWARD_NOMINATION`, `MOVIE_PARENTAL_GUIDE` |
| **Box Office / Distribution / Analytics** | `BOX_OFFICE`, `DAY_ENTRY`, `SHOW_SCHEDULE`, `CENSOR_CERTIFICATE`, `DISTRIBUTION_RIGHT`, `VIEWERSHIP_ANALYTICS` |

Every foreign key relationship in the schema has an explicit `ON DELETE` policy (`CASCADE`, `SET NULL`, or `RESTRICT`) chosen deliberately based on real-world data lifecycle rules — e.g., deleting a `MOVIE` cascades to its cast/crew, genre, and box-office records, but deleting a `PRODUCTION_HOUSE` only nullifies the movie's reference rather than deleting the film itself.



---

## 🧱 Core Entities

| Entity | Purpose |
|---|---|
| **MOVIE** | Central entity — title, release info, budget, links to production house & franchise |
| **PERSON** | Any industry individual (actor, director, composer, lyricist, crew) |
| **ROLE** | Lookup of functions a person can hold on a film |
| **PRODUCTION_HOUSE** | Studio/banner producing films |
| **CONTRACT** | Legal agreement between a person and a production house (remuneration, profit share) |
| **GENRE** | Weighted genre composition per movie |
| **ALBUM / SONG** | Soundtrack structure, linked to music labels |
| **BOX_OFFICE / DAY_ENTRY** | Aggregate & day-wise collection tracking |
| **THEATRE / SHOW_SCHEDULE** | Physical screening infrastructure and scheduling |
| **DISTRIBUTOR / DISTRIBUTION_RIGHT** | Territory-bound distribution rights |
| **CENSOR_BOARD / CENSOR_CERTIFICATE** | Regulatory certification per film, per country |
| **LEGAL_DISPUTE** | Disputes tied to a film and/or a contract |
| **AWARD / AWARD_CATEGORY / AWARD_NOMINATION** | Full award taxonomy and nomination/win tracking |
| **VIEWERSHIP_ANALYTICS** | Region/distributor-level viewership data feeding analytics |
| **MOVIE_PARENTAL_GUIDE** | Content advisory levels (violence, nudity, etc.) per film |



---

## 🔗 How Everything Connects

At the center of CineDB is `MOVIE`. From there, data radiates outward through four major "domains" that all tie back to the same film:

1. **Money flows in:** `PRODUCTION_HOUSE` funds a `MOVIE` → talent is engaged via `CONTRACT` → `CAST_CREW` links `PERSON` + `ROLE` to the film.
2. **Money flows out:** `BOX_OFFICE` (1:1 with `MOVIE`) aggregates collections, broken down daily via `DAY_ENTRY`, and channeled through `DISTRIBUTOR`s who hold time-bound, territory-specific `DISTRIBUTION_RIGHT`s.
3. **The film gets seen:** `CENSOR_CERTIFICATE` clears the film per country/board, `SHOW_SCHEDULE` books it into `THEATRE`s, and `VIEWERSHIP_ANALYTICS` tracks how audiences actually consume it.
4. **The film gets remembered:** its `GENRE` mix, `REVIEW`s, `ALBUM`/`SONG`s (via `MUSIC_LABEL`), and `AWARD_NOMINATION`s all roll back up to build a complete profile — and if a `FRANCHISE` exists, the film's performance contributes to the franchise's cumulative story.
5. **When things go wrong:** `LEGAL_DISPUTE` cuts across both the `MOVIE` and its `CONTRACT`s, letting legal teams trace exactly which agreement or film a dispute originates from.

This means a single query can walk from "an actor's contract" all the way to "the box-office performance and award history of the film that contract was for" — without ever leaving the schema.

---

## ❓ Business Questions This DB Answers

- Which production house has the best ROI over the last 5 years?
- For a given actor, what's their genre portfolio across their career?
- Which films have active legal disputes potentially freezing box-office collections?
- What's the typical certification type for horror-heavy films (>50% genre weight)?
- Which franchise has the highest cumulative worldwide collection across all installments?
- How has the average film budget trended over the last 15 years?
- Which songs crossed a billion streams, and who composed them?
- Which directors have the best award-nomination rate per film?

---

## ⚙️ Setup & Installation

```bash
# 1. Create the database
createdb cinedb

# 2. Run the DDL script
psql -d cinedb -f ddl/ddl_script.sql

# 3. (Optional) Load sample data
psql -d cinedb -f data/sample_data.sql

# 4. Explore
psql -d cinedb
```

The script creates a dedicated `movie_db` schema, all 27 tables with full constraints, and 16 performance indexes on the most frequently filtered/joined columns (e.g., `movie_id`, `person_id`, `release_date`, `recorded_date`, `region`).

---

## 🔍 Sample Queries

```sql
-- Actor's genre portfolio: % of career spent in each genre
SELECT p.full_name, g.genre_name,
       ROUND(AVG(g.weight), 2) AS avg_genre_weight
FROM PERSON p
JOIN CAST_CREW cc ON p.person_id = cc.person_id
JOIN GENRE g ON g.movie_id = cc.movie_id
WHERE p.full_name = 'Actor Name'
GROUP BY p.full_name, g.genre_name
ORDER BY avg_genre_weight DESC;

-- Films with active (unresolved) legal disputes
SELECT m.title, ld.dispute_type, ld.status
FROM MOVIE m
JOIN LEGAL_DISPUTE ld ON m.movie_id = ld.movie_id
WHERE ld.status NOT IN ('Settled', 'Judgment');

-- Franchise-wide cumulative collection
SELECT f.franchise_name,
       SUM(bo.net_collection + bo.overseas_collection) AS cumulative_worldwide
FROM FRANCHISE f
JOIN MOVIE m ON m.franchise_id = f.franchise_id
JOIN BOX_OFFICE bo ON bo.movie_id = m.movie_id
GROUP BY f.franchise_name
ORDER BY cumulative_worldwide DESC;
```

More queries in `queries/sample_queries.sql`.

---

## 🚧 Future Scope

- Ticket booking / POS integration
- Role-based access control & authentication
- Production scheduling (shooting calendars, call sheets)
- Real-time social media metric ingestion
- Payroll & accounting module
- Expansion to TV serials / web series
- Support for international co-productions across multiple studios and countries

---

## 📄 License

This project is open for learning and portfolio purposes. Feel free to fork and build on it.

---

### 👤 Author

Designed and built as an independent database design project — covering the full pipeline from requirements gathering and ER modeling through normalization and SQL implementation.
