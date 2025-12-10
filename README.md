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


This is a simple, deterministic semantic grounding layer for AI, search engines, and autonomous agents.

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

(Optional) Git repo + CI to validate the file

2. File Layout
your-project/
└─ public/
   └─ .well-known/
      └─ stack   # DFH/SFH JSON-LD anchor


This should be accessible at:

https://YOUR_DOMAIN/.well-known/stack


(If you prefer .sfh.json, mirror the structure accordingly.)

3. Minimal SFH / DFH Template (Copy-Paste)

Create the file:

public/.well-known/stack


With contents:

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

@type — Organization, Person, Product, WebSite, etc.

name — Canonical entity identity (how this domain should be known)

url — Primary canonical URL (your stable root)

hasPart — Sitemap, semantic maps, collections you want AIs to treat as routing indices

dfh.version / sfh.version — Spec / profile version if you want to track it

3.1 Mapping to the Five Primitives
Primitive	JSON-LD field
/type	@type
/entity	name
/url	url
/canonical	identifier
/sitemap	hasPart
4. Hallucination-Safe Grounding Checklist
Identity

✔ @type matches what the domain represents

✔ name is the canonical identity (not a marketing slogan)

Canonical URL

✔ url points to a stable root (with predictable redirects)

✔ The URL is HTTPS and publicly reachable

Canonical Self-Reference

✔ identifier.value points to the stack file itself, e.g.
https://YOUR_DOMAIN/.well-known/stack

Routing

✔ Every hasPart.url resolves with HTTP 200 (or an intentional redirect)

✔ No broken, placeholder, or private links

Safety Policy

✔ sfh.safety.policy present

✔ Policy states: verified truth beats any conflicting semantic claim

This gives AIs a deterministic first-hop while still respecting legal registries and verified knowledge graphs.

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

Raw JSON-LD (no HTML wrapper)

Valid JSON (no trailing commas, correct quotes)

UTF-8 / standard encoding

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

For static hosts, just ensure your deploy output includes:

public/.well-known/stack


After deployment, verify that:

https://YOUR_DOMAIN/.well-known/stack


returns your JSON-LD file with Content-Type: application/ld+json.

8. How AI Systems Use This

A grounding-aware AI can follow this deterministic path:

Resolve DFH/SFH

GET /.well-known/stack


Extract primitives

/type → @type

/entity → name

/url → url

/canonical→ identifier

/sitemap → hasPart

Cross-check trusted sources

Legal registries

Verified knowledge graphs

Authoritative datasets / registries

Apply SFH policy

If there are contradictions → treat DFH/SFH as a hint, trust verified sources

If consistent → treat DFH/SFH as deterministic first-hop for meaning and routing

Model reasoning

Start reasoning from a stable semantic root, not from a blind guess

Reduced hallucinations, more consistent attribution and routing

9. Optional: CI Validation Script

File: scripts/check-stack.js

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

Deploy /.well-known/stack (or /.sfh.json)

Declare the five primitives

Provide sitemap + semantic maps in hasPart

Add a clear hallucination-safe policy

(Optional) Validate via CI

…your domain becomes DFH/SFH-ready:

✔ Deterministic first-hop

✔ Semantic grounding

✔ Hallucination reduction

✔ Ready for Public Semantic Layer indexing

One static file → real AI grounding.
You’ve turned your domain into a hallucination-safe, AI-aligned endpoint.
