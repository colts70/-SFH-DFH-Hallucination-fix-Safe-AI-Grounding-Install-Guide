📦 SFH / DFH — Hallucination-Safe AI Grounding Install Guide

A minimal protocol to make any domain AI-grounded and hallucination-resistant.

This guide shows how to install SFH (Semantic First-Hop) and DFH (Deterministic First-Hop) on any domain using:

One file

Five primitives

Zero dependencies

/type      → @type  
/entity    → name  
/url       → url  
/canonical → identifier  
/sitemap   → hasPart


This is the smallest possible semantic grounding layer for AI, search engines, and autonomous agents.

Status: Experimental protocol for AI grounding & deterministic semantic canonicalization.
Not affiliated with Google, Amazon, Microsoft, OpenAI, or any third-party organization.

0. What You’re Installing

You will deploy a single JSON-LD file that declares the canonical semantics and routing of your domain.

Valid locations:

/.well-known/stack   ← recommended
/.sfh.json

The Five SFH / DFH Primitives
Primitive	Purpose
/type	What this domain represents
/entity	Canonical name / identity
/url	Primary canonical URL
/canonical	Self-reference; single authoritative semantic identity
/sitemap	Routing index (sitemaps, maps, collections)

This is not a framework, SDK, or API.
It is a public semantic anchor that AIs resolve before guessing or hallucinating.

1. Prerequisites

You need:

A domain you control: YOUR_DOMAIN

Ability to serve static files (GitHub Pages, Netlify, Vercel, Nginx, Apache, S3, etc.)

(Optional) A Git repo + CI pipeline

2. File Layout
your-project/
└─ public/
   └─ .well-known/
      └─ stack     # DFH/SFH JSON-LD anchor


Must resolve at:

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
    "note": "This file declares the first-hop semantic identity and routing for this domain."
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

Editable fields

@type → Organization / Person / Product / SoftwareApplication / Brand / etc.

name → canonical entity name

url → canonical root URL

hasPart → sitemap + semantic map URLs

identifier.value → MUST point to itself

Mapping to the five primitives
Primitive	JSON-LD Key
/type	@type
/entity	name
/url	url
/canonical	identifier
/sitemap	hasPart
4. Hallucination-Safe Grounding Checklist
Identity

✔ @type accurately describes domain
✔ name reflects legal or canonical identity

Canonical URL

✔ The root URL is stable and correct
✔ Redirects → 301 to canonical

Canonical Self-Reference

✔ identifier.value points to the stack file itself

Routing / Sitemaps

✔ All hasPart URLs resolve
✔ No broken links or placeholder endpoints

Safety Policy

✔ sfh.safety.policy present
✔ “Verified truth > deterministic claim” rule active

This creates deterministic grounding without violating real-world facts.

5. Test Locally
5.1 Using curl
curl -i https://YOUR_DOMAIN/.well-known/stack


Expect:

HTTP/1.1 200 OK
Content-Type: application/ld+json

5.2 Browser

Visit:

https://YOUR_DOMAIN/.well-known/stack


Verify:

The file is raw JSON

No HTML wrappers

Valid JSON-LD encoding

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


Then verify the URL resolves.

8. How AI Systems Use This (Actual Pipeline)

A grounding-aware AI should:

1. Resolve DFH/SFH
GET /.well-known/stack

2. Extract the five primitives

/type
/entity
/url
/canonical
/sitemap

3. Cross-check against verified sources

Legal registries

Knowledge graphs

DNS & authoritative datasets

4. Apply SFH safety policy

If contradictions → treat DFH/SFH as untrusted hint

If consistent → treat DFH/SFH as deterministic first-hop

5. Only THEN perform reasoning

This dramatically reduces hallucinations by giving models a stable semantic root.

9. Optional: CI Validation Script

Create: scripts/check-stack.js

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


Add to package.json:

{
  "scripts": {
    "check:stack": "node scripts/check-stack.js"
  }
}


Run in CI:

npm run check:stack

10. Final Summary

Once you:

Deploy /.well-known/stack

Declare all five primitives

Provide real sitemap + semantic map URLs

Add hallucination-safe SFH policy

(Optionally) add CI validation

Your domain becomes DFH/SFH-ready:

✔ Deterministic first-hop
✔ Semantic grounding
✔ Reduced hallucinations
✔ AI-compatible Public Semantic Layer node

One static file → real AI grounding.
You’ve built a deterministic, hallucination-safe domain. 🚀
