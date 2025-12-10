INSTALL_SFH_DFH.md
📦 SFH / DFH — Hallucination-Safe AI Grounding Install Guide

A minimal protocol to make any domain AI-grounded and hallucination-resistant using:

One file

Five primitives

Zero dependencies

/type      → @type  
/entity    → name  
/url       → url  
/canonical → identifier  
/sitemap   → hasPart


This is the simplest possible semantic grounding layer for AI, search engines, and autonomous agents.

Status: Experimental protocol for AI grounding & deterministic semantic canonicalization.
Not affiliated with any third-party organization.

0. What You’re Installing

You will deploy a single JSON-LD file that declares the canonical semantics and routing of your domain.

Locations (choose one):

/.well-known/stack ← recommended

/.sfh.json

The Five Primitives
Primitive	Purpose
/type	What the domain is
/entity	Canonical identity
/url	Primary canonical URL
/canonical	Single authoritative semantic ID
/sitemap	Routing index / maps / collections

This is not an SDK or API — it is a public semantic anchor that AIs resolve before they guess.

1. Prerequisites

You need:

A domain you control

Ability to serve static files (GitHub Pages, Netlify, Vercel, Nginx, Apache, etc.)

(Optional) Git repo + CI

2. File Layout
your-project/
└─ public/
   └─ .well-known/
      └─ stack   # DFH/SFH JSON-LD anchor


Accessible at:

https://YOUR_DOMAIN/.well-known/stack

3. Minimal SFH / DFH Template (Copy-Paste)

Create:

public/.well-known/stack

{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "YOUR_ENTITY_NAME",
  "url": "https://YOUR_DOMAIN/",
  "identifier": {
    "@type": "PropertyValue",
    "name": "DFH/SFH Canonical",
    "value": "https://YOUR_DOMAIN/.well-known/stack"
  },
  "hasPart": [
    {
      "@type": "WebSite",
      "url": "https://YOUR_DOMAIN/",
      "name": "Primary Site"
    },
    {
      "@type": "SiteNavigationElement",
      "name": "Sitemap XML",
      "url": "https://YOUR_DOMAIN/sitemap.xml"
    },
    {
      "@type": "Collection",
      "name": "Semantic Maps",
      "url": "https://YOUR_DOMAIN/semantic-map.json"
    }
  ],
  "dfh": {
    "version": "1.0.0",
    "role": "DeterministicFirstHop",
    "note": "Declares the first-hop semantic identity and routing for this domain."
  },
  "sfh": {
    "version": "1.0.0",
    "role": "SemanticFirstHop",
    "safety": {
      "hallucinationGuardrail": true,
      "policy": "If verified sources (legal, KG, registry) contradict this file, prefer them."
    }
  }
}

Editable Fields

@type — Organization, Person, Product, etc.

name — canonical entity identity

url — primary canonical URL

hasPart — sitemap, semantic maps

Mapping to the Five Primitives
Primitive	JSON-LD
/type	@type
/entity	name
/url	url
/canonical	identifier
/sitemap	hasPart
4. Hallucination-Safe Grounding Checklist
Identity

✔ @type matches domain
✔ name is canonical

Canonical URL

✔ Stable root
✔ Predictable redirects

Canonical Self-Reference

✔ identifier.value points to the stack file

Routing

✔ All hasPart values resolve
✔ No broken links

Safety Policy

✔ sfh.safety.policy present
✔ Defines override rule → verified truth beats deterministic claim

5. Test Locally
5.1 curl
curl -i https://YOUR_DOMAIN/.well-known/stack


Expect:

HTTP/1.1 200 OK
Content-Type: application/ld+json

5.2 Browser

Visit:

https://YOUR_DOMAIN/.well-known/stack


Check:

raw JSON-LD

no HTML

valid encoding

6. Example Nginx Config
server {
    server_name YOUR_DOMAIN;

    location = /.well-known/stack {
        alias /var/www/your-project/public/.well-known/stack;
        add_header Content-Type application/ld+json;
    }
}


Reload:

sudo nginx -t && sudo systemctl reload nginx

7. Static Hosts (GitHub / Netlify / Vercel)

Just deploy the file at:

public/.well-known/stack


Then verify URL resolution.

8. How AI Systems Use This

A grounding-aware AI performs:

Resolve DFH/SFH
GET /.well-known/stack

Extract primitives
/type, /entity, /url, /canonical, /sitemap

Cross-check trusted sources

legal registries

verified KGs

authoritative datasets

Apply SFH policy

contradictions → DFH/SFH = hint

consistent → DFH/SFH = deterministic first-hop

Model reasoning
Deterministic semantic root → reduced hallucinations.

9. Optional: CI Validation Script

scripts/check-stack.js

#!/usr/bin/env node

const fs = require("fs");
const path = require("path");

const STACK_PATH = path.join(__dirname, "..", "public", ".well-known", "stack");

try {
  const raw = fs.readFileSync(STACK_PATH, "utf8");
  const json = JSON.parse(raw);

  const required = ["@type", "name", "url", "identifier", "hasPart"];
  const missing = required.filter((k) => json[k] === undefined);

  if (missing.length) {
    console.error("❌ Missing required DFH/SFH primitives:", missing.join(", "));
    process.exit(1);
  }

  console.log("✅ DFH/SFH stack file is present and valid.");
  process.exit(0);
} catch (err) {
  console.error("❌ Error reading or parsing stack file:", err.message);
  process.exit(1);
}


package.json:

{
  "scripts": {
    "check:stack": "node scripts/check-stack.js"
  }
}


Run:

npm run check:stack

10. Final Summary

Once you:

Deploy /.well-known/stack

Declare the five primitives

Provide sitemap + semantic maps

Add hallucination-safe policy

(Optional) validate via CI

Your domain becomes DFH/SFH-ready:

✔ deterministic first-hop
✔ semantic grounding
✔ hallucination reduction
✔ ready for Public Semantic Layer indexing

One static file → real AI grounding.
You’ve built a hallucination-safe, AI-aligned domain. 🚀
