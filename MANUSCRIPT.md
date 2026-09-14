# Loop manuscripts

Three loops run on the page. Each is specified here before it is built, and
each is checked against its assertions after. They share the three rules and
nothing else: a loop is one story, and mixing two is what made the first hero
feel arbitrary.

- **Loop 1 — the hero.** Read it later, or listen. 16s.
- **Loop 2 — save it from anywhere.** 9s.
- **Loop 3 — arrange it the way you think.** 19s.

---

# Loop 1 — the hero

The phone on the landing page runs a 16-second loop. This file is the
specification it is built against, and the list of things to check when it
changes. It exists because the first four attempts were judged "awful, sudden,
with no sense of meaning" — and the diagnosis was correct and specific: the
list and the gallery showed different articles, so nothing on screen was
caused by anything before it.

## Three rules

1. **Nothing moves without a cause.** Every transition is preceded by a
   visible tap. No screen ever changes on its own.
2. **Continuity is absolute.** The thing you touch is the thing that responds,
   and it keeps its identity across every transition. One article is the
   protagonist from first frame to last.
3. **Holds are as important as moves.** Every beat rests for at least 1.0s.
   The earlier versions read as sudden because they never stopped.

## The protagonist

**"What a library is for"** — themargin.example, 5 min read, 2 of 5 min left,
teal bar-chart hero. Chosen because it is part-read (so there is a reason to
open it), it carries a distinctive thumbnail (so the eye can track it through
a transition), and it is the fourth row (so the list can scroll to it while it
stays on screen the whole way).

Every frame in the loop is a real screenshot of the real app, captured in one
unbroken session on one simulator against one library. Nothing is composited
from different runs — that is what produced the out-of-sync versions.

## Beats

Times are seconds from loop start. Easing is per segment.

| # | t | dur | what happens | easing |
|---|---|-----|--------------|--------|
| 0 | 0.00 | 1.60 | Inbox at rest, scrolled to top: the Pinned group, then the pile. | — |
| 1 | 1.60 | 1.30 | List scrolls down **118pt**. The protagonist is on screen before and after. | `cubic-bezier(.17,.84,.44,1)` |
| 2 | 2.90 | 1.00 | Hold. The row now sits in the middle of the screen, where a thumb is. | — |
| 3 | 3.90 | 0.12 | Tap indicator on the row. | — |
| 4 | 4.02 | 0.35 | Push. List slides 30% left and dims to 55%; reader slides in from the right edge. | `cubic-bezier(.32,.72,0,1)` |
| 5 | 4.37 | 1.25 | Reader at rest: title, standfirst, byline, the same teal hero. | — |
| 6 | 5.62 | 1.80 | Reader scrolls 671pt — a flick that decelerates. | `cubic-bezier(.17,.84,.44,1)` |
| 7 | 7.42 | 1.00 | Hold, mid-article. | — |
| 8 | 8.42 | 0.12 | Tap indicator on the back button. | — |
| 9 | 8.54 | 0.35 | Pop. Exact reverse of beat 4. | `cubic-bezier(.32,.72,0,1)` |
| 10 | 8.89 | 1.00 | List, **at the same 118pt offset**, same row in the same place. | — |
| 11 | 9.89 | 0.12 | Tap indicator on that row's headphones button. | — |
| 12 | 10.01 | 0.40 | Cross-fade to the playing state: the row's headphones button becomes a pause control with a progress ring around it. | `linear` |
| 13 | 10.41 | 4.40 | Playing. The ring advances. | `linear` |
| 14 | 14.81 | 1.19 | The list scrolls back to the top and the playing state clears — the loop closes on a gesture rather than a cut. | `cubic-bezier(.17,.84,.44,1)` |

**Why 118pt and not a rounder number.** It is where `scrollToItem(.top)`
puts the list back when you leave the article — the restore is by item, not by
pixel offset, so it snaps to a row boundary. Scrolling to any other distance
makes the list jump by the difference on the way back out of beat 9, and an
unexplained jump is exactly the suddenness this document exists to remove.
Measured on device, not assumed: `where.py` correlates a screenshot against the
stitched strip and reports the offset.

**There is no transport island.** The app does not have one on the folder
screen; the row's own control is the whole affordance. An earlier draft of this
manuscript specified one, and it would have been a drawing of something that
does not exist.

## What the beats mean

You come back to a pile you chose (0). You scan it and stop on the one you
were halfway through (1–2) — the progress bar under it is why you stop. You
open it (3–4) and read (5–7). You come back (8–9) and find it where you left
it (10). You would rather walk than read, so you listen instead (11–13), and
it picks up from the row you were just in.

That is the product: things you saved, in the state you left them, readable or
listenable, and nothing arrives that you did not put there.

## Assertions

Checkable in the browser against the built page.

**Continuity**
- The title in the reader (beat 5) is the title of the row tapped in beat 3.
- The hero image in the reader is the row's thumbnail — same teal bar chart.
- The list's scroll offset at beat 10 equals its offset at beat 3 (200pt), so
  the row returns to the same pixel row it left from.
- The row that shows the pause control in beat 12 is the row tapped in beat 3.
- The playing overlay is a crop of that row, positioned where that row sits at
  the 118pt offset — so it cannot drift from the list behind it.

**Causation**
- No layer changes opacity or transform in any interval where no tap indicator
  fired in the preceding 0.2s, except the two scrolls (1, 6) and the ring (13).
- Beat 4 and beat 9 are exact inverses.

**Chrome**
- Exactly one status bar is visible at all times, and it never moves.
- Exactly one search control is visible at any moment. This is why each
  screen owns its own floating chrome and travels with it: two device-fixed
  chromes cross-fading put both search glyphs on screen at half opacity for
  the 350ms of the push.
- The "16 items" pill and "+" sit above their screen's bottom fade, never
  scroll with the list, and travel with the push.
- Each tap ripple has finished before the transition it causes begins — a
  ripple still fading during the push rides the incoming screen and reads as
  part of it.
- The top edge of the scrolling content fades under the nav bar with no visible
  line — no backdrop-filter, whose boundary is itself an edge.

**Geometry** (device is 440 x 956pt; percentages are of 956)
- status band 0 – 62pt (0 – 6.485%)
- nav band 62 – 116pt (6.485 – 12.134%)
- scrolling content window 100 – 822pt (10.46 – 85.98%)
- bottom fade 822 – 956pt
- list strip is 1479pt tall, shipped cropped to 1000pt (all that 118pt of
  scroll can reveal); reader strip 2616pt, shipped cropped to 1560pt
- corner radius 12.5% of width

**Performance**
- Total hero image weight under 300KB. It is 272KB: list 92, reader 161,
  playing row 11, three bars 8. Quality is not the lever — dropping WebP
  quality from 72 to 58 saves 14KB on the reader, because the content is
  small text. Dimensions are the lever, and 880px is 2.9x for a 300px-wide
  device.
- `prefers-reduced-motion: reduce` freezes the loop at beat 2 — the list, the
  protagonist visible and part-read. No push, no reader, no playing state.

---

# Loop 2 — save it from anywhere

**9 seconds.** Under the heading "It starts somewhere else."

The first question a visitor has is not how the app reads; it is how anything
gets into it. The hero answers "then what" without ever answering "from
where", and a reader who does not know the answer does not know what the
product is. This loop is the answer and it is short on purpose.

## The protagonist

**"The half-finished draft"** — slowweb.example. Chosen because it is *not*
in the seeded library: the payoff of this loop is an article arriving, and an
article that was already there cannot arrive. It is served from a local page
in Safari, shared, and lands at the top of the Inbox with an unread dot and
its own cover.

## Beats

| # | t | dur | what happens | easing |
|---|---|-----|--------------|--------|
| 0 | 0.00 | 1.80 | Safari, the article part-scrolled. Real page, real chrome, our own fictional publication. | — |
| 1 | 1.80 | 0.12 | Tap indicator on the share control in the toolbar. | — |
| 2 | 1.92 | 0.40 | The share sheet rises from the bottom edge, the page dimming behind it. | `cubic-bezier(.32,.72,0,1)` |
| 3 | 2.32 | 1.60 | Hold. The sheet is legible, and Notesage is in it. This is the beat the loop exists for. | — |
| 4 | 3.92 | 0.12 | Tap indicator on Notesage. | — |
| 5 | 4.04 | 0.40 | The sheet drops away; the page comes back undimmed. | `cubic-bezier(.32,.72,0,1)` |
| 6 | 4.44 | 0.55 | Hold on Safari — the article is unchanged. Nothing was taken away from where it was. | — |
| 7 | 4.99 | 0.45 | Cut to Notesage's Inbox. The only uncaused move in any loop, and it is a cut rather than a transition for exactly that reason: you switched apps. | `linear` |
| 8 | 5.44 | 2.60 | Hold. **That same article, first row, unread dot, its own cover, no progress bar.** | — |
| 9 | 8.04 | 0.96 | Fade back to Safari. | `ease-in-out` |

## Assertions

- The title on the Safari page (beat 0) is the title of the first Inbox row
  (beat 8), character for character.
- The row's cover is the page's lead image.
- The row shows an unread dot and a plain read-time — never a progress bar.
  It has just arrived; progress on it would be a lie.
- The row is not present anywhere in loop 1's list. If it were, the arrival
  would be invisible.
- Exactly one status bar, and it belongs to whichever app is in front. The
  clock does not change across the app switch.
- Beat 6 exists. Cutting from the tap straight to the Inbox reads as the app
  being taken over; the half-second of unchanged Safari is what makes it read
  as saving rather than leaving.

## Setup this loop needs

The article must not be in the seeded library, and must be reachable from the
simulator — so it is generated as a sixteenth piece, served over local HTTP,
and opened in Safari. `NotesageShare.appex` is in the simulator build, so the
row is real rather than staged.

---

# Loop 3 — arrange it the way you think

**19 seconds.** Under the heading "Your pile, your order."

The longest of the three, and deliberately slow. It shows the one thing a
screenshot cannot: that the arrangement is yours to change, and that changing
it is two taps. It is also where the view menu finally belongs — it had no
motivation inside a reading story, which is why it made the old hero feel
arbitrary.

**The menu is the subject, not a transition.** It holds open for 3.2 seconds
at its first appearance — long enough to read every row rather than long
enough to notice a menu happened. If the beat feels slow while building it,
that is the beat working.

## The protagonist

The library itself. There is no single article this time, so the continuity
anchor is a cover: **"Everything is a draft"**, the pinned one, in its brown
concentric-rectangles cover. It is first under PINNED in the list, first under
PINNED in the gallery, and when the grouping changes it visibly moves — which
is the demonstration, not a break.

## Beats

| # | t | dur | what happens | easing |
|---|---|-----|--------------|--------|
| 0 | 0.00 | 1.60 | Inbox list at the top: PINNED, "Everything is a draft", then ALL NOTES and the pile. | — |
| 1 | 1.60 | 0.12 | Tap indicator on the `…`. | — |
| 2 | 1.72 | 0.30 | The menu springs open from the `…`, scaling from its top-right corner, list dimming behind. | `cubic-bezier(.32,.72,0,1)` |
| 3 | 2.02 | 3.20 | **Hold — the long one.** Every row of the two sections that matter, readable: List / Gallery / Condensed · Alphabetical / Date modified · No grouping / Group by pinned / recent / date / type, with ticks on the active ones. The panel is scrollable on the phone and the image-size and notification rows sit below its edge; they are not what this loop is about. | — |
| 4 | 5.22 | 0.12 | Tap indicator on **Gallery**. | — |
| 5 | 5.34 | 0.28 | The menu collapses back into the `…`. | `cubic-bezier(.32,.72,0,1)` |
| 6 | 5.62 | 0.38 | The list becomes the gallery. Same items, same order, same groups — the anchor cover stays first under PINNED. | `ease` |
| 7 | 6.00 | 3.40 | Hold. Covers, not filenames. | — |
| 8 | 9.40 | 1.30 | The gallery scrolls one row: there are more of them than fit. | `cubic-bezier(.17,.84,.44,1)` |
| 9 | 10.70 | 0.90 | Hold. | — |
| 10 | 11.60 | 0.12 | Tap indicator on the `…`. | — |
| 11 | 11.72 | 0.30 | Menu opens again. | `cubic-bezier(.32,.72,0,1)` |
| 12 | 12.02 | 2.20 | Hold, with the grouping rows in view. Shorter than beat 3 — you have read this menu once. | — |
| 13 | 14.22 | 0.12 | Tap indicator on **Group by date**. | — |
| 14 | 14.34 | 0.28 | Menu collapses. | `cubic-bezier(.32,.72,0,1)` |
| 15 | 14.62 | 0.45 | The gallery regroups: the PINNED header gives way to date headers and the covers reflow. The anchor moves, visibly, because you just asked it to. | `ease` |
| 16 | 15.07 | 3.20 | Hold. Same covers, new order, new headers. | — |
| 17 | 18.27 | 0.73 | Fade back to beat 0. | `ease-in-out` |

## Assertions

**Legibility — the point of the loop**
- Beat 3 holds for ≥ 3.0s and the menu is fully opaque for all of it.
- At the page's rendered size every menu row's text is ≥ 6px tall. If it is
  not, the device is drawn larger for this loop, or the loop is not worth
  having: a menu nobody can read is decoration. Measured at FULL OPEN — the
  panel spends most of the loop scaled to 0.42, and measuring it there says
  4.9px and means nothing.
- Beat 12 holds for ≥ 2.0s.

**Continuity**
- The anchor cover is on screen and first under PINNED in beats 0, 6 and 7.
- Every cover visible in beat 7 is also visible in beat 16 — regrouping
  reorders the same items, it does not fetch different ones.
- The gallery's first row corresponds to the list's first rows: same titles,
  same order. This is the assertion the old hero failed, and it failed it
  because the two were captured from different libraries.

**Causation**
- The menu never opens or closes without a tap indicator in the preceding
  0.2s, and each indicator finishes before the motion it causes.
- The grouping changes only in beat 15, immediately after the tap on beat 13.

**Chrome**
- The `…` stays put through both menu opens — it is the anchor the menu
  scales from, so it must not move.
- The floating pill and `+` stay visible under the open menu, and the list
  behind it is NOT dimmed. Both were checked on the device rather than
  assumed — an earlier draft of this manuscript specified a dim and a hidden
  pill, and the phone does neither.
- The panel is drawn from a crop masked to its own rounded rect. A square
  crop carries the list behind its corners, and those corners then sit over
  the gallery for the 280ms the panel takes to collapse.

## What is deliberately not here

**Search**, which the pill hints at well enough, and **folders and colours**,
which belong with a "your files" story rather than an arranging one. The
natural fourth loop is that one — the same folder open in the Finder on a Mac,
proving the claim the page already makes in words — and it needs a different
capture pipeline, so it is not promised here.

## What this loop needed from the library

"Group by date" put all sixteen items in one bucket called RECENTLY CHANGED,
because the seed script wrote every file at the moment it ran. A library whose
every item changed in the same second is not one anybody has used twice, and
it made the payoff of the loop — the arrangement visibly changing — into the
Pinned header merely disappearing.

The captures now take their modification dates from their own filenames and
the notes are spread over the same weeks, so the grouping produces RECENTLY
CHANGED / SEPTEMBER / AUGUST and the covers genuinely reorder. Same class of
problem as the read times: the library was asserting something the files did
not support.
