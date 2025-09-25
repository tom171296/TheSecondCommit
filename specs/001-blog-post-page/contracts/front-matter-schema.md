# Contract: Blog Post Front Matter Schema

```yaml
required:
  - title
  - date
  - description
  - slug
  - draft
properties:
  title:
    type: string
    minLength: 3
  date:
    type: string
    format: date
  description:
    type: string
    minLength: 10
    maxLength: 300
  slug:
    type: string
    pattern: "^[a-z0-9]([a-z0-9-]*[a-z0-9])$"
  draft:
    type: boolean
  lastmod:
    type: string
    format: date
  tags:
    type: array
    items:
      type: string
additionalProperties: false
``` 

## Validation Rules
1. Slug uniqueness enforced across all files.
2. `lastmod` ≥ `date` if present.
3. Description length within bounds.
4. All required present; absence fails build.
