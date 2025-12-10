INSTALL_SFH_DFH.md

📦 SFH / DFH — Hallucination-Safe AI Grounding Install Guide

A minimal protocol to make any domain AI-grounded and hallucination-resistant.

This guide explains how to install SFH (Semantic First-Hop) and DFH (Deterministic First-Hop) using:

One file

Five primitives

Zero dependencies

The five canonical primitives:

Primitive	JSON-LD Field	Purpose
/type	@type	What this domain is
/entity	name	Canonical name / identity
/url	url	Primary canonical URL
/canonical	identifier	Single authoritative semantic identity
/sitemap	hasPart	Routing index (sitemaps, maps, collections)

This is the simplest possible semantic grounding layer for AI, search engines, and autonomous agents.

Status: Experimental protocol for AI grounding & deterministic semantic canonicalization.
Not affiliated with Google, Amazon, Microsoft, DeepMind, OpenAI, or any third-party organization.

0. What You’re Installing

You will publish one JSON-LD file that declares the deterministic, canonical semantics of your domain.

Valid locations:

/.well-known/stack
/.sfh.json


This file functions as a public semantic anchor that AIs resolve before probabilistic reasoning, embeddings, or hallucination-prone interpretation.

1. Prerequisites

You need:

A domain you control: YOUR_DOMAIN

Ability to serve static files (GitHub Pages, Netlify, Vercel, Nginx, Apache, S3, etc.)

(Optional) Git repo with CI validations

2. File Layout
your-project/
└─ public/
   └─ .well-known/
      └─ stack   # DFH/SFH JSON-LD anchor


Accessed at:

https://YOUR_DOMAIN/.well-known/stack

3. Minimal SFH / DFH Template (Copy-Paste)

Create the file:
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
    "note": "This file declares the deterministic first-hop semantic identity and routing for this domain."
  },
  "sfh": {
    "version": "1.0.0",
    "role": "SemanticFirstHop",
    "safety": {
      "hallucinationGuardrail": true,
      "policy": "If verified sources (legal, KG, registry) contradict this file, prefer the verified source."
    }
  }
}

Editable:

@type: Organization / Person / Product / SoftwareApplication / etc.

name: canonical identity

url: canonical URL of the entity or domain

hasPart: sitemap + semantic maps

Mapping to DFH/SFH primitives:
Primitive	JSON-LD
/type	@type
/entity	name
/url	url
/canonical	identifier.value
/sitemap	hasPart[]
4. Hallucination-Safe Grounding Checklist
Identity

@type accurately describes the entity

name reflects legal or canonical identity

Canonical URL

Root URL is stable

Redirects behave predictably (avoid ambiguity)

Canonical Self-Reference

identifier.value must point to the stack file itself

Routing / Sitemap

All hasPart URLs resolve

No empty placeholders

Safety Policy

sfh.safety.policy is required

Enforces verified truth > deterministic claim

This is the hallucination override rule.

5. Test Locally
5.1 curl
curl -i https://YOUR_DOMAIN/.well-known/stack


Expect:

HTTP/1.1 200 OK
Content-Type: application/ld+json

5.2 Browser

Visit:

https://YOUR_DOMAIN/.well-known/stack


Verify:

Raw JSON-LD loads

No HTML wrappers

UTF-8 encoding

No redirects

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

Just deploy:

public/.well-known/stack


Then verify the URL resolves as JSON.

8. How AI Systems Use This (Actual Pipeline)

A grounding-aware AI will:

Resolve DFH/SFH
GET /.well-known/stack

Extract primitives
/type, /entity, /url, /canonical, /sitemap

Cross-check with trusted sources

legal registries

verified knowledge graphs

authoritative datasets

Apply SFH safety policy

If conflict → treat DFH/SFH as untrusted hint

If consistent → treat DFH/SFH as a deterministic first-hop

Perform model reasoning
(only after grounding)

This reduces hallucinations by providing a deterministic semantic root.

9. Optional: CI Validation Script

/scripts/check-stack.js

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


package.json

{
  "scripts": {
    "check:stack": "node scripts/check-stack.js"
  }
}


Run in CI:

npm run check:stack

10. Final Summary

Once you:

✔ Deploy /.well-known/stack
✔ Declare the five primitives
✔ Provide real sitemap + semantic maps
✔ Add hallucination-safe policy
✔ (Optionally) add CI validation

Your domain becomes DFH/SFH-ready:

Deterministic first-hop semantics

AI grounding

Massively reduced hallucinations

Eligible for Public Semantic Layer indexing

One static file → real AI grounding.
You’ve made your domain hallucination-safe and AI-aligned. 🚀
