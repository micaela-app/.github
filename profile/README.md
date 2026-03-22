# Micaela

> B2B SaaS platform for nutritionists in LATAM — patient management, appointments, clinical measurements and meal plans.

## Repositories

| Repo | Description | Stack |
|------|-------------|-------|
| [api](https://github.com/micaela-app/api) | REST API | Rails 8 · PostgreSQL 16 |
| [web](https://github.com/micaela-app/web) | Frontend app | React 19 · TypeScript · Vite |

## Architecture

```
app.micaela.app          → central login (multi-tenant)
nutrifit.micaela.app     → branded subdomain per tenant
app.nutrifit.pe          → custom domain (BUSINESS plan)
```

- Each client = one **tenant** (nutritional center)
- Auth: JWT in HttpOnly cookie — tenant resolved from user, not URL
- Multitenancy: [acts_as_tenant](https://github.com/ErwinM/acts_as_tenant)

## Plans

| Plan | Price | Professionals | Features |
|------|-------|---------------|----------|
| Free | $0 | 1 | Core features |
| Pro | $19/mo | 3 | Google Calendar · PDF export · Statistics |
| Business | $59/mo | 5 | White label · Custom domain |

## Data Model

```mermaid
erDiagram
  plans {
    string key
    string name
    decimal price
    jsonb features
  }

  tenants {
    string name
    string subdomain
    string country
    int plan_id
  }

  subscriptions {
    int tenant_id
    int plan_id
    string status
    date trial_ends_at
  }

  users {
    int tenant_id
    string first_name
    string last_name
    string email
    string role
  }

  locations {
    int tenant_id
    string name
    boolean is_main
  }

  patients {
    int tenant_id
    string first_name
    string last_name
    string email
    string sex
    date birth_date
    boolean is_active
  }

  patient_tags {
    int tenant_id
    string name
    string color
    boolean is_system
  }

  patient_taggings {
    int patient_id
    int patient_tag_id
  }

  patient_health_profiles {
    int patient_id
    string main_goal
    string activity_level
    string special_condition
  }

  patient_goals {
    int patient_id
    string goal_type
    date target_date
  }

  plans ||--o{ tenants : "has many"
  tenants ||--|| subscriptions : "has one"
  tenants ||--o{ users : "has many"
  tenants ||--o{ locations : "has many"
  tenants ||--o{ patients : "has many"
  tenants ||--o{ patient_tags : "has many"
  patients ||--o{ patient_taggings : "has many"
  patient_tags ||--o{ patient_taggings : "has many"
  patients ||--o| patient_health_profiles : "has one"
  patients ||--o{ patient_goals : "has many"
```

## Stack

```
Backend:      Rails 8 API · PostgreSQL 16
Frontend:     React 19 · TypeScript · Vite · Tailwind CSS v4
Auth:         JWT · HttpOnly cookie · JTI denylist
Storage:      Active Storage · S3
Jobs:         Sidekiq · Redis
Emails:       ActionMailer · SendGrid
Serializers:  Blueprinter
Pagination:   Pagy
```
