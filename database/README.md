# Database Scripts

This folder contains database schema definitions and seed data.

## Structure

- `migrations/` - Versioned schema changes
- `seeds/` - Sample data for development
- `schema.sql` - Complete database schema

## Setup

### Create Database
Create a PostgreSQL database named `steya`.

### Run Schema
Execute `schema.sql` to create all tables.

### Load Seed Data
Run seed scripts for development data.

## Migrations

When making schema changes:
1. Create a new migration file with timestamp
2. Write UP and DOWN migrations
3. Update `schema.sql` with the latest schema

## Note
The application uses SQLAlchemy ORM in `backend/app/models/`.
These SQL files are for reference and initial setup.