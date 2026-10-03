# Writing

Cross-project rules for how any English prose should read: articles,
posts, emails, comments, pull request descriptions, commit bodies,
reports, README introductions, documentation, and this library's own
text. Every project in this family follows these rules for any prose it
produces, whether written by a human or an agent.

Long-form content, articles and posts meant to be read start to finish,
has its own structural rules in [article.md](article.md), which builds on
this file rather than repeating it. This file is the base layer:
argument, vocabulary, typography, cadence, and the patterns that make
prose read as machine-generated regardless of what it is about.

Reference and checklist material (tables, spec templates, bulleted rule
lists, most of this library) follows the typography and vocabulary rules
below. It is exempt from the cadence and argument-shape rules aimed at
narrative writing, except that the sentence or heading above a list
still states what the list shows. See
[Reference and checklist documents](#reference-and-checklist-documents).

## Sources

Wording and sentence-level clarity draw on George Orwell's ["Politics and
the English Language"](https://www.orwellfoundation.com/the-orwell-foundation/orwell/essays-and-other-works/politics-and-the-english-language/)
(1946), on government plain-language standards (the Australian
[Style Manual](https://www.stylemanual.gov.au/blog/basics-plain-language)
and the UK [ONS content guide](https://service-manual.ons.gov.uk/content/writing-for-users/plain-language)),
and on Joseph M. Williams and Joseph Bizup, "Style: Lessons in Clarity
and Grace", which puts the actor in the subject and the action in the
verb, and opens a sentence on what the reader already knows.

What makes a text an argument, a case rather than a row of remarks, draws
on Stephen Toulmin, "The Uses of Argument" (Cambridge University Press,
1958), Barbara Minto, "The Pyramid Principle", Gerald Graff and Cathy
Birkenstein, "They Say / I Say", Wayne Booth, "The Rhetorical Stance"
(1963), and Chaim Perelman and Lucie Olbrechts-Tyteca, "The New Rhetoric"
(1958). Aristotle's "Rhetoric" is the older statement of the same demand:
the reasons that count are the ones this audience can actually be moved
by. George Gopen and Judith Swan, "The Science of Scientific Writing"
("American Scientist", 1990), supply the placement rules, topic position
and stress position, that make the order visible. Links are in
[Further reading](#further-reading).

The same further-reading list covers published work on perplexity and
burstiness, and the field guides for prose that reads as
machine-generated. The em dash allowance in [Typography](#typography) is
the one rule set by precedent in this family. It matches the editorial
voice already published under the family's most content-heavy project. A
rule drawn only from the detection research would have banned the mark.

## Fundamental rule

An assistant drafting content is a writing, revision, and gap-filling tool,
not a co-author. Structure, emphasis, tone, and any choice that changes the
meaning or weight of a text belong to the human who owns that content.

Correct what is grammatically wrong, fill gaps when asked, rephrase awkward
sentences without changing what they say, and ask what is missing instead
of inventing it. Completing a draft without asking what is missing counts
as authoring, which is out of scope unless the project explicitly asks for
it. Inventing the claim is authoring too. When a draft has no claim, ask
what it is.

## Plain English fundamentals

From Orwell and the plain-language tradition, applied to technical writing:

- **Active voice, with the actor as the subject and the action as the
  verb.** "The script validates the input," not "the input is validated
  by the script," and not "validation of the input occurs in the script."
  The first states who does what. The second hides the actor, and the
  third hides the action inside a noun.
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

## Argument

Clarity is how a sentence is built. Rhetoric is the case the sentences
carry, one claim a reader can refuse, the reasons they would accept it,
and an order that shows what each reason does. Articles, posts, emails,
review and issue comments, commit bodies, pull request descriptions, and
any paragraph that introduces a reference page all owe that shape.

Facts, opinions, assertions, and questions earn a place as parts of the
case. A row of them with the relation left unstated is a list, so the
point has to be written in the text.

The claim is one sentence a skeptical reader in this audience could
reject. It states what should be believed or done. A subject-area label
such as "notes on the migration" names a topic. "This is cleaner," with
nothing under it, is an opinion. Stephen Toulmin's working minimum is a
**claim**, the **grounds** that support it, and the **warrant**, the
reason those grounds count as support. Write the warrant whenever this
reader cannot be counted on to supply it. State a limit, or the objection
that would sink the claim, when silence would let the claim say more than
the grounds support.

Say who has to act on the claim, or what follows if it is accepted. That
is the stake, and it belongs on the page. The claim answers a question
this reader has, or would have once the situation is in view. A question
inside the text poses that problem.
Closing on a question is a finished ending only when the claim itself is
that the question is open, and the text says what that openness costs.

Readers take the structure as the emphasis, so the order has to place the
point where they look for it. George Gopen and Judith Swan name the two
positions:

- The opening of a sentence links back to what the reader already holds.
  Sentences that each start on a fresh subject stay a list.
- The point lands at the end of the sentence, the paragraph, and the
  section. That is where a reader places emphasis. A point left in the
  middle is a point the structure discards.

A list belongs under a sentence that says what the items jointly show. A
count such as "there are three reasons" still needs that sentence, which
is Barbara Minto's test for a governing thought. The same shape scales to
the whole document. One governing claim, then the reasons, then the
grounds under each reason. Where the reader still needs the context, open
with a situation they accept and the complication that makes the claim
the question to answer, then give the claim.

When the source material has no claim, ask for it. Choosing the point of
view is authoring, which the [fundamental rule](#fundamental-rule) keeps
with the human who owns the text.

A figure of speech has to carry a reason. Antithesis, triads, and
punchlines that carry none stay banned under
[Banned structural patterns](#banned-structural-patterns).

## Emails, comments, and short posts

The shape stays the same at any length. A shorter text shows less ground.

- **Email, and any message that asks for an action.** The subject line
  carries the claim or the ask. The first sentence restates it with the
  reason, and the message makes one ask. The rest is the grounds the
  recipient needs in order to act.
- **A comment** on a review, an issue, or a post. One claim, why it
  matters in this thread, and the evidence at hand: a line, a quoted
  passage, a result. A reaction, a question with no stake, or a stack of
  separate objections leaves the thread with nothing it can use.
- **A short post.** One claim, the warrant, and enough grounds to check
  the warrant. A post long enough to need sections follows
  [article.md](article.md), where each section is itself a reason.

## Documentation is timeless

A README, and anything under `docs/`, describes what the thing is and how
it works, in the present tense, for a reader who has no idea when it was
written. It does not narrate what happened to the project. No dated status
section, no "recently", no "we have now migrated", no account of what the
code used to be before somebody changed it.

That history is a project-management asset and it already has three homes
that are better at holding it: git, the changelog, and the issue tracker.
Copying it into documentation means every document quietly expires, and a
reader cannot tell which sentences are still true.

| Instead of | Write |
|---|---|
| The repository was reset in September 2026, replacing a .NET study lab | Nothing, or, if the reader genuinely needs to know the project is early, "no application code has landed yet" |
| We recently migrated from X to Y | The system uses Y |
| This module is currently being refactored | Nothing. An issue tracks the refactor |
| As of this writing, the API returns three fields | The API returns three fields |

Two kinds of date survive. A claim about something outside the project
carries one, because the date is what tells a reader when to recheck it:
"`group_imports` remains nightly-only as of September 2026" is useful
precisely because it will expire and says so. And a changelog is a history
by definition, which is why the history belongs there.

## Cadence and connected prose, not staccato

The clearest tell of machine-generated prose is rhythm, not vocabulary.
Short, punchy sentences stacked in a row, each landing with the same
weight, read as machine cadence even when every fact is correct.

Connected prose joins ideas through subordinate clauses and ordinary
connectors (*because*, *while*, *which*, *when*, *so*, *although*) instead
of dropping bare fragments next to each other and leaving the reader to
infer the relationship. That connector is the warrant from
[Argument](#argument), and the next sentence should open on something the
previous one already gave the reader. Compare:

- Fragmented: "New role. New codebase. One week in."
- Connected: "One week into a new role and a new codebase, the parts that
  slow me down are not the parts I expected."

Vary sentence length on purpose. Every sentence should still be a
complete, connected thought, and five short sentences in a row is a
drumbeat. Published work on AI-generated text calls that unevenness
**burstiness** and calls predictable word choice low **perplexity**. Aim
for a long, clause-heavy sentence followed by a short one, then another
extended one, using a word a competent reader would not have predicted.
Uniform length and the single most likely next word read as generated
even when the grammar is perfect. The limits of both measures
as a self-check are under
[Signals of AI-generated prose](#signals-of-ai-generated-prose).

## Typography

Most of these are mechanical and apply everywhere, including headers,
titles, and table cells, not only in flowing prose. The em dash rule is
the one exception, scoped to body prose.

- **Em dashes, used sparingly, are fine in flowing prose.** An occasional
  one is fine; a paragraph full of them is not, and a chain of em dashes
  should never stand in for rewriting a run of short, choppy sentences.
  Prefer a comma, period, colon, or parentheses as the default. Never use
  an em dash in a title, heading, or product name (see
  [naming.md](../style/naming.md#user-facing-text)).
- **No en dashes.** They read as a typo of a hyphen to most readers and
  earn their place far less often than an em dash does; use a plain
  hyphen or rewrite instead.
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
  [Cadence](#cadence-and-connected-prose-not-staccato). Sentence length and
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

## Compression tools

A tool that compresses agent output into telegraphic fragments, caveman
being the one this family has looked at, contradicts nearly every rule
above at once: connected prose, cadence, plain sentence construction.

Use one only where the output is thrown away, meaning exploration,
scratch reasoning, a conversation nobody will read twice. Switch it off for
anything that gets committed: documentation, specs, commit messages, pull
request descriptions, code comments. If the tool cannot be scoped that way,
it does not belong in a repository whose prose this file governs.

Check whether it is genuinely off rather than assuming. A plugin that
registers a `SessionStart` hook is active from installation, and its
default mode may well be its most aggressive one. See
[integrations/toolkits.md](../integrations/toolkits.md).

## Reference and checklist documents

Most files in this library are reference material: tables, bulleted rule
lists with a bold lead-in term, spec and PR templates, checklists. That
format is the clearest way to present a list of rules or a checklist, and
it is exempt from the colon-subtitle and repeated-template bans above,
which target narrative prose pretending to be a list, not an actual list.

The typography rules (no contractions, no banned vocabulary, straight
quotes, no en dash) still apply in reference documents, including inside
table cells and headers, and headers and table cells never take an em
dash either, the same as a title. So does plain, active-voice sentence
construction within any introductory paragraph. A one-line description
next to a bullet term is not narrative prose and does not need to satisfy
the cadence rules; a three-paragraph introduction to a spec document does,
including the occasional-em-dash allowance above.

The argument rule still applies one level up. The heading or the sentence
above a list states what the items jointly show, and the bullets are the
grounds, so the list can stay a list. The introduction, when a reference
page has one, argues for the page the way any other prose does.

## Self-review checklist

Run this before finishing any draft, whether it is an email, a comment,
a report, or a one-paragraph pull request description:

1. State the claim in one sentence a skeptical reader could reject, and
   say who acts on it or what follows if it is accepted. A draft that is
   only facts, opinions, assertions, or questions is still a list. Ask
   for the claim, or cut until one remains. On a reference page, the
   claim is the sentence or heading the list hangs from.
2. For each reason, confirm the warrant is on the page or already held by
   this reader. Any list should sit under a sentence that says what the
   items show, and the point of a paragraph should fall in its last
   sentence.
3. Scan for every word in [Banned vocabulary](#banned-vocabulary); replace
   each with something specific to this sentence.
4. Check every header, title, and table cell for an em dash, and check the
   body for more than an occasional one; check everywhere for en dashes,
   curly quotes, and the ellipsis character. Replace with plain
   equivalents.
5. Scan for English contractions and spell them out in full.
6. Check for any "not X, but Y" or "it sounds like X, but Y" construction
   and rewrite it as a direct statement.
7. Check for a list padded or trimmed to exactly three items for
   rhetorical effect.
8. Check for disconnected fragments dropped next to each other; connect
   them with a conjunction that states why they belong together.
9. If explaining several points in a row, confirm they do not all share
   one sentence template.
10. Read the draft once for rhythm: if every sentence lands with the same
    weight, combine or split until it varies.
11. Cut throat-clearing openers unless the human author wrote them on
    purpose.
12. For a README or a document under `docs/`, scan for dates, "recently",
    "currently", and any sentence describing what the project used to be.
    Cut them: that belongs in git and the changelog.

## Further reading

The works below are the ones the rules above come from.

- [Politics and the English Language, George Orwell (1946)](https://www.orwellfoundation.com/the-orwell-foundation/orwell/essays-and-other-works/politics-and-the-english-language/)
- [Style: Lessons in Clarity and Grace, Joseph M. Williams and Joseph Bizup](https://en.wikipedia.org/wiki/Style:_Lessons_in_Clarity_and_Grace)
- [The Science of Scientific Writing, George Gopen and Judith Swan, American Scientist 78.6 (1990)](https://www.cs.tufts.edu/comp/105-2016s/readings/sci.html)
- [The Uses of Argument, Stephen Toulmin (Cambridge University Press, 1958; updated edition 2003)](https://doi.org/10.1017/CBO9780511840005)
- [The Minto Pyramid Principle, Barbara Minto](https://www.barbaraminto.com/concept)
- [They Say / I Say, Gerald Graff and Cathy Birkenstein (W. W. Norton)](https://wwnorton.com/books/they-say-i-say/)
- [The Rhetorical Stance, Wayne Booth, College Composition and Communication 14.3 (1963)](https://www.jstor.org/stable/355049)
- [The New Rhetoric, Chaim Perelman and Lucie Olbrechts-Tyteca (Presses Universitaires de France, 1958; English translation, University of Notre Dame Press, 1969)](https://undpress.nd.edu/9780268004460/the-new-rhetoric/)
- [Rhetoric I.2, Aristotle, translated by J. H. Freese](http://www.perseus.tufts.edu/hopper/text?doc=Perseus:text:1999.01.0060:book=1:chapter=2)

The field guides below cover prose that reads as machine-generated.

- [What is perplexity and burstiness for AI detection?, GPTZero](https://gptzero.me/news/perplexity-and-burstiness-what-is-it/)
- [How to Tell if Writing is AI](https://huntingthemuse.net/library/how-to-tell-if-writing-is-ai)
- [Cleaning Up AI Prose: An Editorial Rulebook for Agents](https://thinkwright.ai/editorial-standards)
- [A Field Guide to Terrible AI Writing](https://medium.com/@tdoherty_96508/a-field-guide-to-terrible-ai-writing-6a83ddb6a141)
