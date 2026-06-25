# PieceTracker

**Capstone Project Proposal, Step Two**

## Overview

Orchestras and ensembles play multiple pieces every season, typically two seasons per year, but most have no centralized place to track repertoire history, concert programming order, part-specific difficulty, or performance notes. That information ends up scattered across notebooks, spreadsheets, email threads, and memory.

PieceTracker is a centralized, shared web application built for my own orchestra that lets members log repertoire, track each instrument part's difficulty and notes, link to sheet music, and maintain a complete performance history across seasons.

## Tech Stack

- **Frontend:** React
- **Backend:** Node.js with Express
- **Database:** MongoDB (Mongoose)

## Focus

This will be an evenly focused full-stack application. There's meaningful work on both ends: backend data modeling and authentication, and frontend search, browse, and edit interfaces for the repertoire library.

## Project Type

A website, accessible to orchestra members through login.

## Goal

PieceTracker's goal is to give my orchestra a single, shared source of truth for repertoire: what's been played, when, in what order, at what difficulty per part, and with what notes. It replaces scattered personal records with one collaborative, searchable system.

## Users

Primary users are members of my orchestra, a small group, who need to log in, browse repertoire, and contribute updates. All logged-in members have equal edit access. There is no librarian or admin role distinction for the MVP, which matches how the orchestra actually operates.

As a stretch goal, the data model is designed so it could expand to support other orchestras or ensembles as separate, isolated groups in the future.

## Data

Data is entirely self-sourced. There is no external API. Orchestra members are the data source, entering pieces, parts, difficulty ratings, and performance history directly. This avoids the data quality risks tied to scraped or third-party API data, since the content is authoritative and comes from the people who actually performed it.

## Database Schema

The schema is document-based (MongoDB) and reflects how orchestras actually function: a single piece has multiple instrument parts (for example Violin 1, Violin 2, Flute 1), each with its own difficulty and notes, since difficulty isn't uniform across a piece. Performance history is tracked per piece across seasons.

```
Piece {
  title: String,
  composer: String,
  notes: String,              // general notes: cuts, entrances, arrangement
  parts: [{
    instrumentPart: String,   // e.g. "Violin 1", "Flute 2"
    scoreUrl: String,         // link to sheet music (e.g. shared Drive folder)
    difficulty: String,       // Easy / Medium / Hard, specific to this part
    notes: String,            // part-specific notes (tricky passages, etc.)
    addedBy: ObjectId (ref -> User)
  }],
  performances: [{
    season: String,           // e.g. "Fall 2025"
    concertOrder: Number,
    date: Date
  }],
  addedBy: ObjectId (ref -> User),
  createdAt: Date,            // auto via Mongoose timestamps
  updatedAt: Date
}

User {
  username: String,
  passwordHash: String,
  instruments: [String],      // instrument(s) this member plays
  role: String                // "member" (flat for MVP, no admin tier)
}
```

## Potential Issues

- **Data consistency:** Since data is entered by multiple members rather than pulled from an API, season names and difficulty labels could become inconsistent over time. I plan to mitigate this with dropdowns or enums for standardized fields like difficulty and season, instead of free text.
- **Nested data editing:** Parts live nested inside each Piece document, which takes more careful CRUD logic than flat fields would, but it's a good real-world MongoDB modeling pattern to learn.

## Sensitive Information

No sensitive personal data is collected. The only credential that needs standard security practice is the user password, which will be hashed (for example with bcrypt) before storage. Repertoire and performance data aren't sensitive.

## Functionality (MVP)

- User signup and login, with all members having equal, full edit access
- Add, edit, and delete pieces, including per-part details such as instrument, difficulty, score link, and notes
- Log performances per piece (season, concert order, date)
- Search and filter the repertoire library by title, composer, instrument, or difficulty, case-insensitive
- View full part breakdown and performance history for any piece
- User profile shows which instrument(s) a member plays

## User Flow

Login, then the repertoire library (a searchable, filterable list of pieces), then selecting a piece to view its part breakdown, difficulty per part, notes, and performance history, and from there adding or editing parts, notes, or logging a new performance.

## What Makes This More Than CRUD: Stretch Goals

- **Saved Folders:** Curated collections of pieces, like "Fall 2025 Season" or "Violin Repertoire," for quick access.
- **Multi-orchestra support:** Separate groups with isolated repertoire libraries.
- **Season builder:** A drag and drop tool to plan concert order, with flags for pieces repeated from recent seasons.
- **Programming suggestions:** Surfacing pieces or composers that haven't been played in a long time.
