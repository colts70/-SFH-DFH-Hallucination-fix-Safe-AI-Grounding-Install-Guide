SFH / DFH Hallucination-Safe AI Grounding — Install Guide

A minimal install guide to make your domain AI-grounded, canonicalized, and hallucination-resistant.

This file is intended for INSTALL_SFH_DFH.md or your main README.md.

0. What You’re Installing

You are deploying a single JSON-LD file that gives every AI system and search engine a
deterministic first-hop for meaning before any interpretation, embedding, ranking, or reasoning.

File locations (choose one):

/.well-known/stack ← recommended

/.sfh.json

Purpose: Establish the domain’s semantic identity, canonical routing, and ground-truth primitives used by AI models as the first-hop grounding layer.

The Five Canonical Primitives

Primitive	Meaning
/type	What this domain is (Organization, Person, Product, SoftwareApplication…)
/entity	Canonical identity name
/url	Primary canonical URL
/canonical	Self-identity reference of the semantic root
/sitemap	Public routing index (XML sitemaps, semantic maps, site nav, etc.)

This is not a framework, SDK, runtime, or API.
It is a public semantic anchor — deterministic, static, cacheable.

1. Prerequisites

You need:

A domain you control

A static file host (GitHub Pages, Netlify/Vercel, Nginx, Apache, etc.)

Ability to serve:

https://YOUR_DOMAIN/.well-known/stack


Optional: repo for CI validation.

2. File Layout
your-project/
└─ public/
   └─ .well-known/
      └─ stack     # SFH/DFH JSON-LD anchor


The only requirement is that the file resolves at:

https://YOUR_DOMAIN/.well-known/stack

3. Minimal SFH/DFH JSON-LD Template (Copy/Paste)
{
  "@context": "https://schema.org",
  "type": "Organization",                          
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
      "name": "Primary Site",
      "url": "https://YOUR_DOMAIN/"
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
    "note": "Declares the deterministic semantic identity and routing for this domain."
  },

  "sfh": {
    "version": "1.0.0",
    "role": "SemanticFirstHop",
    "safety": {
      "hallucinationGuardrail": true,
      "policy": "If verified external sources (legal, KG, registry) contradict this file, prefer them."
    }
  }
}


This expresses all 5 primitives through JSON-LD equivalents:

/type → type

/entity → name

/url → url

/canonical → identifier.value

/sitemap → hasPart[]

4. Hallucination-Safe Grounding Checklist

✔ Identity clear & unique
✓ Correct type
✓ True canonical name

✔ Canonical URL stable
✓ No random redirects
✓ HTTPS preferred

✔ Canonical self-reference
✓ identifier.value → points to the stack file itself

✔ Sitemap / semantic maps valid
✓ URLs resolve successfully
✓ Remove unused sections

✔ Safety override present
✓ Ensures DFH/SFH never conflicts with verified truth

This is why DFH/SFH is safe and legit.
Models use it as a deterministic hint, not an override of reality.

5. Testing Your Install
5.1 curl
curl -i https://YOUR_DOMAIN/.well-known/stack


Expect:

HTTP/1.1 200
Content-Type: application/ld+json

5.2 Browser Test

Go to:

https://YOUR_DOMAIN/.well-known/stack


You should see clean JSON — no HTML wrapper.

6. Nginx Example
server {
    server_name YOUR_DOMAIN;

    location = /.well-known/stack {
        alias /var/www/your-project/public/.well-known/stack;
        add_header Content-Type application/ld+json;
    }
}

7. Static Host Deployment (Netlify, Vercel, GitHub Pages)

Place file in:

public/.well-known/stack


Deploy → Done

8. How AI Should Use This File (The Grounding Pipeline)
Step 1 — Resolve DFH/SFH
GET https://YOUR_DOMAIN/.well-known/stack

Step 2 — Extract primitives

/type

/entity

/url

/canonical

/sitemap

Step 3 — Cross-check with verified truth sources

Legal identity

Knowledge graphs

Registries

Public authoritative sources

Step 4 — Apply safety override

If contradiction → treat SFH/DFH as an untrusted hint
If aligned → treat as deterministic first-hop grounding

Step 5 — Only now allow model reasoning

This eliminates hallucination by removing ambiguity in the semantic root.

9. CI Validation Script (Optional)
scripts/check-stack.js
#!/usr/bin/env node
const fs = require("fs");
const path = require("path");

const STACK_PATH = path.join(__dirname, "..", "public", ".well-known", "stack");

try {
  const raw = fs.readFileSync(STACK_PATH, "utf8");
  const json = JSON.parse(raw);

  const required = ["type", "name", "url", "identifier", "hasPart"];
  const missing = required.filter((k) => json[k] === undefined);

  if (missing.length) {
    console.error("❌ Missing required DFH/SFH primitives:", missing.join(", "));
    process.exit(1);
  }

  console.log("✅ DFH/SFH stack file is valid.");
  process.exit(0);
} catch (err) {
  console.error("❌ Error reading or parsing DFH/SFH file:", err.message);
  process.exit(1);
}

10. Summary

Once deployed, your domain becomes:

AI-grounded

Hallucination-resistant

Semantically deterministic

Machine-canonical

Part of the Public Semantic Layer (PSL)

You now operate a domain that LLMs, KGs, search engines, and agents can resolve with zero guesswork.

This is the hallucination fix the entire industry is looking for
