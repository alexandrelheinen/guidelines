# Article

Structural rules for long-form published content: articles, in-depth
posts, and anything meant to be read start to finish by someone outside
the project. This file builds on [writing.md](writing.md): the vocabulary,
typography, and cadence rules there apply here without repetition. This
file only adds what is specific to long-form structure.

## Sources

Structural guidance below draws on the newswriting tradition (the inverted
pyramid and its feature-writing alternative) and on the shared clarity
principles behind the Google Developer Documentation Style Guide and the
Microsoft Writing Style Guide: know the audience, establish a distinctive
voice, and make every word earn its place.

## Before drafting

- **The angle is explicit.** One sentence states what happened and why it
  matters to the reader, not only to the author.
- **The scope is bounded.** The piece covers one arc: problem, attempt,
  outcome. A side quest belongs in another piece or a short aside, not a
  detour in this one.
- **Facts are verified** against live behavior and vendor documentation,
  not memory or assumption.
- **Know the reader.** A piece for practitioners in the same field can use
  the field's real vocabulary; a piece for a general audience needs the
  jargon translated on first use, not dropped in and left for the reader
  to look up.

## Choosing a lead

Two structures cover most technical writing, and the choice should be
deliberate rather than default:

- **Inverted pyramid.** State the outcome and its cost or constraint in
  the first paragraph, then add supporting detail in descending order of
  importance. Use this for anything closer to an announcement, a status
  update, or a reference piece, where a reader who stops after one
  paragraph should already have the key fact. This is the right default
  for most engineering writing, since readers scan before they commit to
  reading in full.
- **Delayed, narrative lead.** Open on a concrete scene or moment before
  stating the point, then earn the reveal. This suits a personal or
  feature-style narrative where the story's shape is part of what the
  piece is arguing. A delayed lead still has to reach the stakes within
  the first two or three paragraphs; delaying past that reads as
  withholding, not building tension.

Whichever structure is chosen, lead with something concrete: an outcome, a
number, a moment, never a throat-clearing preamble about what the article
is about to cover.

## Structure

- **Sections follow chronology or causality, not tool categories.** Prefer
  "why X failed" before "how Y works," organized around the actual
  sequence of events or reasoning, not a table of contents assembled by
  topic.
- **Headings are specific.** "Cloudflare Pages" beats "The solution." No
  colon-subtitle headings (see
  [writing.md](writing.md#banned-structural-patterns)).
  A heading should tell the reader what is in the section, not tease it.
- **One idea per section.** If a section's sentences average more than two
  subordinate clauses each, split it.
- **Tables and diagrams earn their place.** A table compares alternatives
  or settings the reader must actually reproduce; a diagram shows a flow
  the prose would otherwise take a full page to describe. Neither belongs
  in a piece as decoration.
- **A glossary is optional**, and only earns a place when a term repeats
  across multiple sections. Define a term used once at its first
  occurrence in the body instead.
- **No hollow conclusion.** Do not restate the introduction. End on what
  is still open, what it cost, or what you would do differently next
  time.

## Concrete over abstract

Lead with credit counts, deploy times, error messages, and public URLs
before reaching for an abstract framing of what they mean. A reader
remembers "the build took eleven minutes and failed on the third retry"
far longer than "the build process had significant reliability issues."

## Cross-links

At least one link should connect a new piece to related writing already
published, when a relevant piece exists. A reader arriving at one article
should be able to find the two or three others that share its context.

## Writing about restricted work

Some technical work sits under a non-disclosure agreement or otherwise
cannot be described in detail. Do not fill that gap with vague corporate
language about impact or innovation. Describe the shape of the engineering
problem instead of the confidential system: the kind of constraint, the
kind of tradeoff, without naming what cannot be named.

## Do not expose private structure

An article read on the open web should never carry internal organization
details: repository paths, folder names, script or workflow filenames,
configuration keys, secret names, exact build commands copied from a
pipeline, or internal hostnames, unless the piece has been explicitly
approved for that level of detail. Describe behavior and architecture in
generic terms (a static site, a content pipeline, an object store, a
browser runtime) and point the reader to vendor documentation rather than
to a private repository tree.

## Pre-publish checklist

- [ ] Angle, scope, and audience are all explicit (see
      [Before drafting](#before-drafting)).
- [ ] Lead states a concrete outcome or moment within the first two
      paragraphs.
- [ ] Structure follows chronology or causality, not a topic list.
- [ ] No colon-subtitle headings; each heading states what is in the
      section.
- [ ] Every table and diagram earns its place (see
      [Structure](#structure)).
- [ ] No internal paths, filenames, or hostnames leaked into the text (see
      [Do not expose private structure](#do-not-expose-private-structure)).
- [ ] Cross-links exist to related published pieces where relevant.
- [ ] The [writing.md self-review checklist](writing.md#self-review-checklist)
      has been run on the full draft.
- [ ] The piece was read once for rhythm after the self-review pass, since
      structural fixes can reintroduce a uniform cadence.
