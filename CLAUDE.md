# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

**Depot** is a Rails 8.1 e-commerce app built while following the book *Agile Web Development with Rails 8* (Pragmatic Bookshelf). It sells programming books and is currently in active development, implementing chapters iteratively.

## Common commands

```bash
bin/dev                        # Start server + Tailwind watcher (Procfile.dev)
bin/rails test                 # Run all tests
bin/rails test test/models/product_test.rb        # Run one test file
bin/rails test test/models/product_test.rb:15     # Run one test by line
bin/ci                         # Full CI: setup, rubocop, security scans, tests, seed check
bin/rubocop                    # Lint Ruby code
bin/brakeman --quiet --no-pager # Static security analysis
bin/rails db:migrate           # Run pending migrations
bin/rails db:seed              # Seed products (deletes all first, attaches images from db/images/)
```

## Architecture

**Stack:** Rails 8.1, SQLite3, Tailwind CSS (via tailwindcss-rails), Propshaft, Hotwire (Turbo + Stimulus), importmap-rails, Active Storage, Solid Cache/Queue/Cable, Kamal for deployment.

**Current models:**
- `Product` — title, description, price (decimal 8,2), image (Active Storage `has_one_attached`). Validates presence of all fields, uniqueness of title, price ≥ 0.01, image must be GIF/JPG/PNG. Broadcasts `broadcast_refresh_later_to "products"` after commit for Turbo Stream live updates.

**Controllers:**
- `ProductsController` — full CRUD for admin product management
- `StoreController` — storefront index (in progress, no root route set yet)

**Testing:** Minitest with fixtures. Fixture file `test/fixtures/products.yml` includes `:one`, `:two`, and `:pragprog` records. Image upload tests use fixture files from `test/fixtures/files/` (lorem.jpg, logo.svg, etc.). Controller tests use `file_fixture_upload` for multipart form submissions with images.

**Seed data:** `db/seeds.rb` clears all products then creates three book records, attaching images from `db/images/`. Run with `bin/rails db:seed` or the CI `db:seed:replant` step.
