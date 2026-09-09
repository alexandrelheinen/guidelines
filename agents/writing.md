# Writing

Cross-project rules for how any English prose should read: PR descriptions,
commit bodies, reports, README introductions, documentation, and this
library's own text. Every project in this family follows these rules for
any prose it produces, whether written by a human or an agent.

Long-form content specifically, articles, posts, and similar published
pieces, has its own structural rules in [article.md](article.md), which
builds on this file rather than repeating it. This file is the base layer:
vocabulary, typography, cadence, and the patterns that make prose read as
machine-generated regardless of what it is about.

Reference and checklist material (tables, spec templates, bulleted rule
lists, most of this library) follows the typography and vocabulary rules
below but is exempt from the prose-structure rules aimed at narrative
writing. See [Reference and checklist documents](#reference-and-checklist-documents).

## Sources

This guideline draws on George Orwell's ["Politics and the English
Language"](https://www.orwellfoundation.com/the-orwell-foundation/orwell/essays-and-other-works/politics-and-the-english-language/)
(1946), plain-language writing standards used in government style guides
(the UK's [Style Manual](https://www.stylemanual.gov.au/blog/basics-plain-language)
and [ONS content guide](https://service-manual.ons.gov.uk/content/writing-for-users/plain-language)),
current published research on AI-generated text detection covering
perplexity and burstiness, and the field guides linked in
[Further reading](#further-reading).

## Fundamental rule

An assistant drafting content is a writing, revision, and gap-filling tool,
not a co-author. Structure, emphasis, tone, and any choice that changes the
meaning or weight of a text belong to the human who owns that content.

Correct what is grammatically wrong, fill gaps when asked, rephrase awkward
sentences without changing what they say, and ask what is missing instead
of inventing it. Completing a draft without asking what is missing counts
as authoring, which is out of scope unless the project explicitly asks for
it.

## Plain English fundamentals

From Orwell and the plain-language tradition, applied to technical writing:

- **Active voice.** "The script validates the input," not "the input is
  validated by the script." Active voice states who does what and removes
  ambiguity about responsibility.
- **The short word over the long one, and the plain word over the jargon
  term**, unless the technical term is the one the reader actually needs
  (a protocol name, a function name).
- **Cut every word that adds nothing.** If a sentence still says the same
  thing with a clause removed, remove the clause.
- **Do not open a sentence with an empty construction** ("There is a
  script that...", "It is the case that..."). Name the subject and give it
  a verb directly: "A script validates..."
- **One idea per sentence.** A sentence trying to hold two unrelated
  claims usually needs to become two sentences, or one sentence with a
  subordinate clause that states how the claims connect, rather than a
  semicolon papering over the seam.
- Orwell's closing rule applies here too: break any rule on this page
  sooner than produce something clumsy. These are defaults, not a
  mechanical filter.

## Cadence: connected prose, not staccato

The clearest tell of machine-generated prose is rhythm, not vocabulary.
Short, punchy sentences stacked in a row, each landing with the same
weight, read as machine cadence even when every fact is correct.

Connected prose joins ideas through subordinate clauses and ordinary
connectors (*because*, *while*, *which*, *when*, *so*, *although*) instead
of dropping bare fragments next to each other and leaving the reader to
infer the relationship. Compare:

- Fragmented: "New role. New codebase. One week in."
- Connected: "One week into a new role and a new codebase, the parts that
  slow me down are not the parts I expected."

Vary sentence length on purpose, but every sentence should still be a
complete, connected thought. Five short sentences in a row is a drumbeat,
not a voice. Published research on AI-generated text describes this
property as **burstiness**: human writing varies sentence length and
structure noticeably from one sentence to the next (a long, clause-heavy
sentence followed by a short one, then another extended one), while
generated text tends toward uniform length and a repeated
subject-verb-object shape. A companion measure, **perplexity**, captures
how predictable each word choice is; consistently picking the single most
likely next word reads as flat even when it is grammatically perfect. Aim
for writing a competent reader would call surprising in its word choices
and uneven in its rhythm, in the way an actual person's writing is uneven,
not for writing that hits a uniform, safe average everywhere.

## Typography

These are mechanical and apply everywhere, including headers, titles, and
table cells, not only in flowing prose.

- **No em dashes or en dashes**, anywhere. Use a comma, period, colon,
  parentheses, or a plain hyphen instead.
- **Straight quotes only.** No curly single or double quotes.
- **No ellipsis character.** If a genuine ellipsis is needed, type three
  plain periods.
- **No decorative symbols** in place of standard ones: no special bullets,
  non-breaking spaces, or arrow glyphs where a plain hyphen or "->" reads
  the same.
- **Spell out contractions in English**: "it is" not "it's," "do not" not
  "don't," "cannot" not "can't." This rule governs English contractions
  specifically; it does not extend to other languages where an equivalent
  short form is grammatically required rather than stylistic (French
  elisions such as "l'article").

## Banned vocabulary

Words below are not forbidden because they are wrong. They are forbidden
because they have become filler: a reader's eye slides past them without
learning anything a plainer word would not have said just as well.
Replace with something specific to the sentence at hand.

| Category | Words |
|---|---|
| Verbs | delve, leverage, utilize, foster, navigate, embark, unlock, unleash, unravel, harness, empower, facilitate, optimize, streamline, elevate, enhance, bolster, underscore (as a verb), showcase, spearhead, revolutionize, cultivate, champion, illuminate, demystify, embrace, resonate, transcend, propel, catalyze, galvanize, orchestrate, curate, supercharge, reimagine, redefine, amplify, unveil, garner |
| Adjectives | crucial, pivotal, vital, essential, paramount, integral, robust (as empty praise), comprehensive, multifaceted, nuanced, intricate, seamless, dynamic, vibrant, transformative, groundbreaking, cutting-edge, state-of-the-art, unprecedented, invaluable, indispensable, profound, compelling, meticulous, holistic, bespoke, unparalleled, ever-evolving, fast-paced, game-changing, myriad, boundless, enduring |
| Metaphors and nouns | tapestry, landscape (as an abstract noun), realm, journey, beacon, labyrinth, symphony, mosaic, cornerstone, testament, paradigm, synergy, ecosystem (outside its literal biological or software-dependency sense), framework (as filler), roadmap, treasure trove, game-changer, powerhouse, plethora, zeitgeist, bastion, odyssey, crossroads, frontier, horizon, catalyst, linchpin, bedrock, backbone (as in "backbone of X"), "deep dive" |
| Hedges and transitions | furthermore, moreover, additionally, consequently, nevertheless, nonetheless, thus, hence, in essence, in conclusion, in summary, ultimately, "it is worth noting that," generally speaking, arguably, presumably, "while it is true that" |
| Stock openers and closers | "In today's fast-paced world," "as technology continues to evolve," "whether you are an X or a Y," "let us dive in," "imagine if," "picture this," "in conclusion," "at the end of the day," "looking forward to sharing more" |

## Banned structural patterns

- **Contrast reframe.** "It is not just X, it is Y," or "It sounds like a
  small detail, but..." A manufactured tension used to sound insightful.
  State the point directly.
- **Negative parallelism.** "Not A, but B," or "It's not about X, it's
  about Y." State what is true and let the contrast live in the argument.
- **Forced triads.** Padding or trimming a list to exactly three items
  because three sounds rhetorical. Use however many items are actually
  true.
- **Repeated sentence templates.** Explaining several points with the
  identical shape each time is a listicle wearing prose clothing. Vary the
  phrasing and length, and say which point matters most instead of
  presenting them as equals.
- **Disconnected fragments.** Bare, unconnected clauses dropped next to
  each other for punch, forcing the reader to guess how they relate. Join
  them with a connector that states the relationship.
- **Both-sides hedging.** Presenting balanced pros and cons to avoid
  committing to a view, when the context calls for an actual position.
- **Fake specificity.** "Picture this," or "As a developer, you know..."
  Generic appeals to shared experience. Use a real, concrete detail
  instead: a name, a number, a place, an actual example.
- **Colon-subtitle structures.** "Short phrase: elaboration" as a
  sentence, headline, or bullet. The pattern is common in generated text
  and rarely earns its place; state the point directly instead of staging
  it.
- **Mic-drop closers.** A three-to-six-word sentence used only as a
  punchline after a fuller paragraph. Fold it into the preceding sentence
  or cut it.
- **Faux-insider openers.** "Here's the thing," or "What nobody tells
  you." These perform secrecy instead of making a claim.
- **Hollow endings.** A closing section that only restates what the
  reader already read. End on what is still open, what it costs, or what
  you would do differently.

## Signals of AI-generated prose

Beyond individual banned words, these are structural signals drawn from
published AI-text-detection research, useful as a self-check even without
a detector:

- **Low burstiness and low perplexity.** See
  [Cadence](#cadence-connected-prose-not-staccato). Sentence length and
  structure that stay in a narrow band across a whole passage, and word
  choices that are always the single most predictable one, both read as
  machine-generated independent of subject matter.
- **Uncited authority claims.** A claim of the form "studies show" or
  "experts agree" with no source attached. One or two are normal in a long
  piece; a passage where more than half of its claims float free of any
  source is a strong tell.
- **Removable filler.** If cutting a third of a paragraph's sentences
  loses no information, the paragraph was padded, not written. Reread and
  cut, rather than trim after the fact.

Two caveats worth keeping in mind, since detection research itself flags
them: formal or academic writing produced by an actual person can score
low on burstiness too, so these are prompts for a human self-check, not a
verdict to run through a detector and trust blindly. And a reader who
works with these models daily gets noticeably better at spotting the
patterns than any automated tool, which is exactly why this file exists
instead of relying on a scanner.

## Reference and checklist documents

Most files in this library are reference material: tables, bulleted rule
lists with a bold lead-in term, spec and PR templates, checklists. That
format is the clearest way to present a list of rules or a checklist, and
it is exempt from the colon-subtitle and repeated-template bans above,
which target narrative prose pretending to be a list, not an actual list.

The typography rules (no em dashes, no contractions, no banned vocabulary,
straight quotes) still apply in reference documents, including inside
table cells and headers. So does plain, active-voice sentence
construction within any introductory paragraph. A one-line description
next to a bullet term is not narrative prose and does not need to satisfy
the cadence rules; a three-paragraph introduction to a spec document does.

## Self-review checklist

Run this before finishing any draft, whether it is a report or a
one-paragraph PR description:

1. Scan for every word in [Banned vocabulary](#banned-vocabulary); replace
   each with something specific to this sentence.
2. Check for em dashes, en dashes, curly quotes, and the ellipsis
   character, in the body, headers, and titles alike; replace with plain
   equivalents.
3. Scan for English contractions and spell them out in full.
4. Check for any "not X, but Y" or "it sounds like X, but Y" construction
   and rewrite it as a direct statement.
5. Check for a list padded or trimmed to exactly three items for
   rhetorical effect.
6. Check for disconnected fragments dropped next to each other; connect
   them with a conjunction that states why they belong together.
7. If explaining several points in a row, confirm they do not all share
   one sentence template.
8. Read the draft once for rhythm: if every sentence lands with the same
   weight, combine or split until it varies.
9. Cut throat-clearing openers unless the human author wrote them on
   purpose.

## Further reading

- [Politics and the English Language, George Orwell (1946)](https://www.orwellfoundation.com/the-orwell-foundation/orwell/essays-and-other-works/politics-and-the-english-language/)
- [What is perplexity and burstiness for AI detection?, GPTZero](https://gptzero.me/news/perplexity-and-burstiness-what-is-it/)
- [How to Tell if Writing is AI](https://huntingthemuse.net/library/how-to-tell-if-writing-is-ai)
- [Cleaning Up AI Prose: An Editorial Rulebook for Agents](https://thinkwright.ai/editorial-standards)
- [A Field Guide to Terrible AI Writing](https://medium.com/@tdoherty_96508/a-field-guide-to-terrible-ai-writing-6a83ddb6a141)
