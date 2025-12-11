AI Grounding File — Install Guide

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

0. What You’re Installing

You will deploy one JSON-LD file that declares the canonical semantics and routing of your domain.

Valid locations:

/.well-known/stack ← recommended

/.sfh.json

This is not an SDK, API, or framework.
It is a public semantic anchor that AIs resolve before they guess.

1. Prerequisites

You need:

A domain you control

Ability to serve static files (GitHub Pages, Netlify, Vercel, Nginx, Apache, S3, Cloudflare, etc.)

(Optional) Git repo + CI to validate the file

2. File Layout
your-project/
└─ public/
   └─ .well-known/
      └─ stack   # DFH/SFH JSON-LD anchor


This must be reachable at:

https://YOUR_DOMAIN/.well-known/stack


(If using .sfh.json, mirror exactly.)

3. Minimal SFH / DFH Template (Copy-Paste)

Create the file:

public/.well-known/stack


Contents:

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
Field	Purpose
@type	What the domain represents (Organization, Person, Product, WebSite, etc.)
name	Canonical identity for this domain
url	Primary canonical URL
hasPart	Routing indices: sitemap, collections, semantic maps
identifier	Canonical self-reference (must point to this file)
dfh / sfh	Optional protocol profile metadata
3.1 Mapping to the Five Primitives
Primitive	JSON-LD Field
/type	@type
/entity	name
/url	url
/canonical	identifier
/sitemap	hasPart
4. Hallucination-Safe Grounding Checklist
Identity

✔ @type correctly describes the domain
✔ name is canonical (not marketing fluff)

Canonical URL

✔ url uses HTTPS
✔ Stable root (no random redirects)

Canonical Self-Reference

✔ identifier.value = https://YOUR_DOMAIN/.well-known/stack

Routing / Maps

✔ All hasPart.url return HTTP 200
✔ No broken links
✔ Sitemap exists

Safety

✔ sfh.safety.policy included
✔ Policy states: verified reality beats semantic claims

This ensures grounding without pretending to override truth.

5. Test Locally
5.1 curl
curl -i https://YOUR_DOMAIN/.well-known/stack


Expect:

HTTP/1.1 200 OK
Content-Type: application/ld+json

5.2 Browser

Visit the URL. Verify:

Raw JSON

No HTML wrapper

Valid UTF-8

No errors

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

Just ensure your deploy output contains:

public/.well-known/stack


Then verify:

https://YOUR_DOMAIN/.well-known/stack

8. How AI Systems Use This

A grounding-aware AI follows this deterministic chain:

1. Resolve

GET → /.well-known/stack

2. Extract Primitives

/type → @type

/entity → name

/url → url

/canonical → identifier

/sitemap → hasPart

3. Cross-Check

AIs check:

legal registries

verified knowledge graphs

authoritative datasets

4. Apply SFH policy

If contradictions → trust verified sources
If consistent → treat this file as first-hop ground truth

5. Model reasoning

Starts from deterministic semantics →
fewer hallucinations, safer routing, better attribution.

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

✔ Deploy /.well-known/stack (or .sfh.json)
✔ Declare the five primitives
✔ Provide routing via hasPart
✔ Include hallucination-safe policy
✔ (Optional) Validate via CI

Your domain becomes DFH/SFH-ready:

Deterministic first-hop

Semantic grounding

Hallucination reduction

Ready for Public Semantic Layer indexing

One static file → real AI grounding.
Your domain becomes a hallucination-safe, AI-aligned endpoint.

🌐 DFH / SLPI: Optional 10-Anchor Extension for High-Trust Domains
Why Most Companies Only Need 5 Anchors — And Why Some Need All 10

DFH / SLPI is designed around a minimal, deterministic core that any domain can deploy in under a minute.
This core is the 5-Anchor Meaning Layer, which communicates the what, who, and where of a domain in a machine-verifiable way.

But for high-trust industries — research, journalism, finance, government, legal, scientific publishing — meaning alone is not enough.

They also need provenance.

This post explains the optional 10-Anchor Extension, why it exists, and who should use it.

✅ The 5-Anchor Meaning Layer (Default, Recommended for 99% of Domains)

These anchors define the deterministic identity of your domain:

Anchor	Purpose
/type	What kind of thing this domain represents
/entity	The entity’s unique identity
/url	The domain’s authoritative location
/canonical	The canonical name / label / ID
/sitemap	The surface area the domain exposes

This layer is:

minimal

universal

backward-compatible

enough for all AI systems to lock onto meaning deterministically

For most companies, this is all that is required for full DFH compliance.

Deploy it → and your domain becomes AI-readable, indexable, and meaning-stable.

🔐 The Optional 10-Anchor Extension (For Heavy Hitters Only)
The Provenance Layer: “Why Trust It?”

Some domains must provide more than meaning.
They must provide verifiable provenance — the origin, lineage, and trust properties of what they publish.

For those cases, DFH offers the optional† provenance anchors:

Anchor	Purpose
/source	Who asserted the identity or claim
/derivation	What the content was derived from
/history	How it changed over time
/license	Legal permissions for use
/integrity	Checksums / signatures for tamper-proofing

These anchors are recommended for:

scientific institutions

academic research domains

financial or regulatory bodies

news and journalism outlets

legal or compliance-critical systems

archival, preservation, and distributed-trust networks

These organizations rely on traceability, auditability, and chain-of-custody guarantees — which standard 5-anchor identity cannot provide.

🧩 Layered by Design: Minimal Core, Optional Trust Expansion

DFH’s architecture deliberately follows a layered protocol approach:

Layer 1 = Meaning (Anchors 1–5)

Mandatory

Minimal

Zero dependencies

Layer 2 = Provenance (Anchors 6–10)

Optional

High-trust domains only

Adds cryptographic and lineage semantics

This keeps DFH:

simple for everyday use

industrial-strength for organizations that need it

future-proof for AI grounding and regulatory frameworks

🏁 Summary

If you run a typical business or website → deploy Anchors 1–5.

If you publish knowledge that must be trusted, verified, or regulated → extend to Anchors 6–10.

One protocol. Two layers. Universal adoption, optional provenance.

This is the cleanest and most scalable model:
maximum simplicity for the web, maximum trust for the institutions that require it.
