# Article

Structural rules for long-form published content: articles, in-depth
posts, and anything meant to be read start to finish by someone outside
the project. This file builds on [writing.md](writing.md). Vocabulary,
typography, cadence, and the argument rules there apply here without
repetition. This file adds the arrangement a piece of that length needs:
one claim, reasons in an order, and grounds a reader can check.

## Sources

Arrangement below draws on the newswriting tradition (the inverted
pyramid and its feature-writing alternative), on Barbara Minto's pyramid
(one governing thought, the reasons beneath it, the evidence beneath
each reason), and on George Gopen and Judith Swan's account of where a
reader looks for the point. Claim, warrant, and stake are defined in
[writing.md](writing.md#argument). Audience and wording follow the
[Google developer documentation style guide](https://developers.google.com/style)
and the [Microsoft Writing Style Guide](https://learn.microsoft.com/en-us/style-guide/welcome/).
Know who is reading, and make every word earn its place.

## Before drafting

- **The claim is one sentence.** It says what the reader should accept or
  do, and a skeptical reader in this audience could reject it. "Notes on
  the migration" names a topic. The claim is the judgment about it.
- **The stake is on the page.** Who has to act on the claim, or what
  follows if it is accepted.
- **The scope is one argument.** The piece covers one arc: the question,
  the claim, the reasons, and the limit. A side quest belongs in another
  piece or a short aside.
- **Facts are verified** against live behavior and vendor documentation,
  so the grounds are something the reader can check.
- **Know the reader.** A piece for practitioners in the same field can use
  the field's real vocabulary, and can leave a shared warrant implicit. A
  piece for a general audience translates the jargon on first use and
  writes the warrant out.

## Choosing a lead

Two structures cover most technical writing, and the choice should be
deliberate rather than default:

- **Inverted pyramid.** State the claim and its stake in the first
  paragraph, then the reasons and grounds in descending order of
  importance. Use this for an announcement, a status update, or a
  reference piece, where a reader who stops after one paragraph should
  already hold the claim. This is the right default for most engineering
  writing, since readers scan before they commit to reading in full.
- **Delayed, narrative lead.** Open on a concrete scene, then reach the
  claim within the first two or three paragraphs. This suits a personal
  or feature-style narrative where the path to the claim is part of the
  argument. Waiting longer withholds the point.

Whichever structure is chosen, lead with something concrete: an outcome, a
number, a moment, never a throat-clearing preamble about what the article
is about to cover.

## Structure

- **Sections are the reasons, in the order the argument needs.** That
  order is cause, deduction, or time. Use time when earlier events are
  why the claim holds. A section opens with its reason and then gives the
  grounds. The heading can name the subject. The first sentence is still
  the reason.
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
- **No hollow conclusion.** End on what is still open, what it cost, or
  what you would do differently next time. Restating the introduction,
  or posing a question the piece was written to answer, leaves that
  ending empty.

## Concrete over abstract

Lead with credit counts, deploy times, error messages, and public URLs.
Those details are the grounds. The sentence that says what they show is
the reason, and it has to be on the page. A reader remembers "the build
took eleven minutes and failed on the third retry" far longer than "the
build process had significant reliability issues."

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

- [ ] The claim is one sentence a skeptical reader could reject, and the
      stake is named (see [Before drafting](#before-drafting)).
- [ ] The lead reaches that claim within the first two paragraphs.
- [ ] Each section opens with the reason it contributes, then the grounds.
      The order is cause, deduction, or time, whichever the warrant
      requires.
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
