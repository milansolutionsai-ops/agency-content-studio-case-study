# Agency Content Studio

**An internal content platform that took me out of a loop I was the bottleneck in.**

Case study. The source is private (it's internal agency tooling and it consumes private design-system packages), so this documents what it is, the decisions worth arguing about, and what changed.

---

## The problem

Every headline, price and testimonial on the agency's marketing sites was a code change. A two-second copy decision from the marketing team became a developer's pull request, a review and a deploy. Across several sites in two languages, I was a blocking dependency on work that required no engineering judgment whatsoever.

The obvious answer is a CMS. The obvious answer is also how marketing sites get destroyed: give non-developers free-form rich text and a page structure, and within a month the layouts are broken and nobody can say when.

## What I built

A Next.js platform that makes the agency's own marketing sites editable by non-developers, without giving anyone the ability to break a page.

**Slot-based content model.** Pages are composed of named slots rather than free-form rich text. Editors fill slots. They cannot restructure a page or break a layout, because the structure was never theirs to edit. This is the whole design in one sentence.

**Preview-first editing.** Six purpose-built editors, each rendering into the real component rather than an approximation. You see the actual result before publishing, so "it looked fine in the editor" stops being a category of bug.

**Edit overlay on the live site.** Fixing a typo doesn't route through a separate admin tool. You're on the page, you edit the page.

---

## Two decisions worth defending

**The public content API holds no secrets, on purpose.**

Content destined for a public web page is not a secret. Guarding it would have invented a credential-management problem in exchange for protecting information that is, by definition, about to be published. So the consuming site needs exactly one environment variable and holds no credentials at all. That deleted a whole class of problem rather than solving it: no key rotation, no leaked-token incident, nothing to provision when a new site is added.

**Signed revalidation instead of a shared token.**

There is one genuinely privileged operation: drop the cache and re-fetch. That endpoint verifies an **Ed25519 signature** rather than a shared secret. The consequence is that requesting a refresh can be a public operation without becoming a way for anyone to hammer the site into rebuilding on demand.

The pattern generalises: work out which operation is actually privileged, protect that one properly, and stop pretending the rest needs protecting.

---

## Outcome

- Live in production. The whole team edits all marketing sites.
- I am out of the copy-change loop, which was the entire point.
- No credentials deployed to any consuming front end.
- Ships in lockstep with the main marketing site, consuming the private design-system packages (tokens, brand, blocks, primitives, preset) from a package registry.

**Stack:** Next.js · React · TypeScript · Supabase · Vercel · GitHub Packages

Powers the marketing sites of the digital agency it was built for.

---

More of my work: [milan-solutions.vercel.app](https://milan-solutions.vercel.app) · [LinkedIn](https://www.linkedin.com/in/milan-veljkovikj)
