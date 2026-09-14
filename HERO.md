# Hero animation manuscript

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

## Second loop, further down the page

Separate, no continuity with the hero, because it is a different story:
**save from anywhere.** Safari with an article → share sheet → Notesage → the
article at the top of the Inbox, unread. Six seconds, one protagonist of its
own. It lives under its own caption, where the caption can explain it.

The view menu and the gallery belong to a third moment — "find it again" —
for the same reason: switching views has no motivation inside the reading
story, and putting it there is what made the old hero feel arbitrary.
