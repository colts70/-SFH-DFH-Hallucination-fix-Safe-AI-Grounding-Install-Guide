📦 SFH / DFH — Hallucination-Safe AI Grounding Install Guide

A minimal protocol to make any domain AI-grounded and hallucination-resistant.

This guide shows how to install SFH (Semantic First-Hop) and DFH (Deterministic First-Hop) on any domain using:

One file

Five primitives

Zero dependencies

This is the simplest possible semantic grounding layer for AI, search engines, and autonomous agents.

Status: Experimental protocol for AI grounding & deterministic semantic canonicalization.
Not affiliated with Google, Amazon, Microsoft, OpenAI, or any third-party organization.

0. What You’re Installing

You will create a single JSON-LD file that declares the canonical semantics and routing of your domain.

Locations (choose one):

/.well-known/stack
/.sfh.json


The Five Primitives

Primitive	Purpose
/type	What this domain is
/entity	Canonical name / identity
/url	Primary canonical URL
/canonical	Single authoritative semantic identity
/sitemap	Routing index (sitemaps, maps, collections)

This is not a framework, SDK, or API.
It is a public semantic anchor that AIs resolve before guessing or hallucinating.

1. Prerequisites

You need:

A domain you control: YOUR_DOMAIN

Ability to serve static files (GitHub Pages, Netlify, Vercel, Nginx, Apache, anything)

(Optional) A Git repo + CI

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


Editable fields:

@type: Organization / Person / Product / SoftwareApplication / etc.

name: canonical entity name

url: canonical URL

hasPart: sitemap + semantic maps

Mapping to the five primitives:
@type = /type
name = /entity
url = /url
identifier = /canonical
hasPart = /sitemap

4. Hallucination-Safe Grounding Checklist
Identity

@type correctly describes the domain

name matches the legal / canonical identity

Canonical URL

The root URL is stable

Redirects behave predictably

Canonical Self-Reference

identifier.value points to the stack file itself

Sitemap / Routing

All hasPart URLs resolve

Remove broken or placeholder entries

Safety Policy

Ensure sfh.safety.policy is present

This creates the hallucination override rule
→ verified truth > deterministic claim

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

no HTML wrappers

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

8. How AI Systems Use This (Actual Pipeline)

A grounding-aware AI should:

Resolve DFH/SFH
GET /.well-known/stack

Extract primitives
/type, /entity, /url, /canonical, /sitemap

Cross-check with trusted sources

legal registries

verified knowledge graphs

authoritative datasets

Apply SFH safety policy
If contradictions → DFH/SFH = untrusted hint
If consistent → DFH/SFH = deterministic first-hop

Only then perform model reasoning

This dramatically reduces hallucinations by giving models a deterministic semantic root.

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


Run in CI:

npm run check:stack

10. Final Summary

Once you:

Deploy /.well-known/stack

Declare the five primitives

Provide real sitemap + semantic maps

Add hallucination-safe policy

(Optionally) add CI validation

Your domain becomes DFH/SFH-ready:

✔ deterministic first-hop
✔ semantic grounding
✔ reduced hallucinations
✔ ready for Public Semantic Layer indexing

One static file → real AI grounding.
You’ve built a hallucination-safe, AI-aligned domain. 🚀
