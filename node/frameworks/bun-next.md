Running locally

```bash
bun --bun run dev
```

Installation with Bun

```bash
bun create next-app
bun add --dev prettier prettier-plugin-tailwindcss prettier-eslint
bun x --bun shadcn@latest init --dev
```

Create a `.prettier-rc`

```json
{
  "trailingComma": "es5",
  "tabWidth": 2,
  "semi": false,
  "singleQuote": true,
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

```bash
bun x --bun shadcn@latest add table button input checkbox context-menu table dialog label scroll-area select skeleton toggle textarea
bun add @tanstack/react-table
```

Drizzle ORM

```bash
bun add drizzle-orm pg dotenv @neondatabase/serverless
bun add --dev drizzle-kit tsx @types/pg
```
