SFH / DFH — Hallucination-Safe AI Grounding Install Guide

A minimal protocol to make your domain AI-grounded and hallucination-resistant.

This guide shows you how to install SFH (Semantic First-Hop) and DFH (Deterministic First-Hop) on any domain using:

One file

Five primitives

Zero dependencies

Who this is for:
Anyone who owns a domain and wants AIs and search engines to resolve a deterministic, safe first-hop for meaning before model reasoning.

Status: Experimental protocol for AI grounding & semantic canonicalization.

0. What You’re Installing

You will deploy a single JSON-LD file that declares the canonical semantics of your domain.

Location:

/.well-known/stack


or:

/.sfh.json


The five primitives:

/type      → What this domain represents  
/entity    → Canonical name / identity  
/url       → Primary canonical URL  
/canonical → Single semantic identity (self-reference)  
/sitemap   → Routing index (sitemaps, semantic maps, collections)


This is not a framework or SDK.
It is a public semantic anchor that AIs, search engines, and agents resolve before guessing.

1. Prerequisites

You need:

A domain you control: YOUR_DOMAIN

Ability to serve static files (any of):

GitHub Pages

Netlify / Vercel

Nginx / Apache

Any host that can expose /.well-known/stack

(Optional) A Git repo for versioning and CI

2. File Layout

Add the file in your project like:

your-project/
└─ public/
   └─ .well-known/
      └─ stack      # DFH/SFH JSON-LD anchor


The URL must resolve to:

https://YOUR_DOMAIN/.well-known/stack

3. Minimal DFH / SFH Template (Copy-Paste)

Create the file:

public/.well-known/stack

with:

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


You may edit:

@type — Person / Organization / Product / SoftwareApplication / etc.

name — canonical entity name

url — canonical domain root

hasPart — sitemap + semantic maps

The five primitives map to:
@type, name, url, identifier, hasPart.

4. Hallucination-Safe Grounding Checklist

Check these before deploying:

Identity

@type correctly describes the domain

name is the canonical or legal entity identity

Canonical URL

url is stable

Redirects behave predictably

Canonical Self-Identity

identifier.value points to the stack file itself

Sitemap

Any URLs listed in hasPart must resolve

Remove sitemap entries that don't exist

Safety

sfh.safety.policy is present

Ensures alignment: verified truth > deterministic claim

5. Test Locally
5.1 curl
curl -i https://YOUR_DOMAIN/.well-known/stack


You should see:

HTTP/1.1 200 OK

Content-Type: application/ld+json

Clean JSON-LD body

5.2 Browser

Visit:

https://YOUR_DOMAIN/.well-known/stack


Confirm:

No HTML wrapper

No encoding issues

Valid JSON-LD

6. Example: Nginx Config
server {
    server_name YOUR_DOMAIN;

    location = /.well-known/stack {
        alias /var/www/your-project/public/.well-known/stack;
        add_header Content-Type application/ld+json;
    }
}


Reload:

sudo nginx -t && sudo systemctl reload nginx

7. Static Hosts (GitHub, Netlify, Vercel)

Just place:

public/.well-known/stack


Deploy normally.

Verify:

https://YOUR_DOMAIN/.well-known/stack

8. How AI Systems Use This (Conceptual Pipeline)

A grounding-aware agent should:

1. Resolve DFH/SFH
GET /.well-known/stack

2. Extract the five primitives

@type

name

url

identifier

hasPart

3. Cross-check against external truth

Legal registries

Verified KGs

Regulatory data

Other high-trust references

4. Apply SFH safety policy

If conflicting:
→ Treat DFH/SFH as an untrusted hint

If consistent:
→ Use DFH/SFH as the deterministic first-hop for meaning

5. Only then run model reasoning

This pipeline drastically reduces hallucinations by giving models a deterministic root before semantic expansion.

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

10. Summary

Once you have:

Deployed /.well-known/stack

Declared the five primitives

Linked your real sitemap/semantic maps

Added a hallucination-safe policy

(Optionally) added CI

Your domain becomes DFH/SFH-ready:

AIs resolve a deterministic first-hop

Hallucinations drop due to explicit grounding

Your domain now participates in the Public Semantic Layer

No frameworks, no APIs — just one static file

You now operate a hallucination-aware, AI-grounded domain using SFH / DFH. 🚀
