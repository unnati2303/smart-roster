# Approach Note — SmartRoster Ops

## Problem & Domain Modeling
SmartRoster Ops models multi-brand D2C customer experience and operations across 32 employees, 4 operational pods, 20 brands (80% single-employee ownership, 20% multi-employee split or overlapping process ownership), and distinct processes such as Customer Support, Abandoned Cart Conversions, NDR, and Retention. Operating calendars (365-day, daily excluding public holidays, and Sundays + public holidays off) are modeled separately from individual employee weekly offs, planned leaves, comp-offs, and preferences.

## Scheduling & Validation Architecture
- **Generator (`src/engine/generator.js`):** Builds monthly rosters per brand, process, and date while respecting operating calendars, employee assignments, weekly-off rules, approved leaves/comp-offs, and staffing requirements.
- **Validator (`src/engine/validator.js`):** Evaluates every generated schedule and live manual edit against hard constraints (mandatory client coverage, leave/holiday rules, staffing minimums) versus soft constraints (preferred off-days), surfacing gaps and conflicts explicitly rather than silently producing an invalid roster.
- **Persistence & UI (`src/App.jsx`, `src/lib/supabase.js`):** Connects a React + Vite interface with Supabase so operations managers can inspect views, edit shifts live, simulate leave/unavailability changes, and export CSVs.

## Key Trade-Offs & Decisions
- **Explicit conflict surfacing over silent auto-fixing:** When coverage cannot be satisfied—such as when a sole brand owner takes leave on an operating day—the system flags a hard coverage gap and supports what-if simulation rather than silently dropping coverage or overriding human approvals.
- **Client-side validation on the current dataset:** Keeping schedule generation and validation responsive in the browser works cleanly for ~32 employees and 20 brands; scaling to a much larger headcount and brand catalog would move heavy constraint solving and paginated queries to backend/database services.