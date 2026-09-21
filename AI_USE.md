# AI Use Disclosure

Author: Aayush Shah

This project used Claude (Anthropic) as a coding and research assistant
throughout development, working interactively in a chat session, the same
way as Assignment 2.

## What AI was used for

- Brainstorming the initial notebook structure end to end: the Guardian API news
  collection code, the FRED and Yahoo Finance data pulls, the
  heteroskedasticity based IV estimator, and the three replicated tables.
- Debugging real problems found while actually running the notebook,
  including: GDELT's free API being unusably rate limited, which led to
  switching the news source to the Guardian Open Platform API instead;
  a date parsing bug from GDELT's non-standard date format; a Guardian API
  pagination cap that silently truncated results for popular keywords,
  fixed by chunking the date range into weekly windows; an unquoted
  multi word Guardian query matching on the word "of" and massively
  inflating the Strait of Hormuz keyword's article count; and an over
  correction to exact phrase matching that then missed real coverage,
  fixed by switching to Guardian's AND operator instead.
- Catching and fixing a real formula bug in the IV estimator, where the
  numerator and denominator of the pi1 and pi2 estimators from Rigobon and
  Sack (2003) equations (6) and (7) had been inverted, which was producing
  unstable, near zero t-statistics across every variable. This was caught
  by noticing the t-statistics were uniformly close to zero in a way that
  did not look like a real null result.
- Diagnosing why the corrected estimator still produced unstable,
  sign flipping coefficients: the two year Treasury yield, the
  normalization variable used in the original paper, turned out to have
  essentially no variance shift between high news and low news days in
  this sample. This was checked directly rather than assumed, and led to
  switching the normalization variable to oil futures, which did show a
  clear variance response.
- Fixing a bug in the Table 3 variance decomposition formula, where an
  early version used a simple pooled full sample variance instead of the
  day count weighted formula from the original paper, producing the wrong
  direction of the relationship between the high news day percentage and
  the full period percentage.
- Searching for and compiling the event descriptions in Table 1, using
  Wikipedia's 2026 Iran war timeline, the Congressional Research Service,
  CFR's Daily News Brief archive, ACLED's Middle East updates, Britannica,
  and CSIS reporting, matched to the specific dates flagged as high news
  days by the classifier.
- Drafting the report text, the benchmark comparison against the 2003
  paper, and the discussion of whether heteroskedasticity based
  identification is the right method here, using the notebook's actual
  output numbers.
- Building the Word document itself (font, table formatting, page setup)
  and rendering it to check that every table actually fit on the page
  and displayed correctly before handing it back.

## What was NOT AI-generated / required my own judgment

- Getting a Guardian API key and setting it up locally, and deciding to
  keep it out of the repository with an env file rather than hardcoding
  it, since this repository is public.
- Editing and actually running the notebook end to end multiple times, 
  including the final restart and Run All check, and catching that the 
  notebook I had been told was finished had in fact stopped partway 
  through due to the duplicate cell bug described above.
- Deciding to normalize the estimator on oil futures instead of the two
  year Treasury yield once the variance check came back the way it did,
  rather than forcing the original paper's variable choice to work.
- Reading through the compiled Table 1 event descriptions and the report
  text for accuracy before including them.
- The framing decision to treat this sample as measuring an already
  ongoing war rather than pre-war risk, and to call this out explicitly
  as a difference from the 2003 paper rather than presenting the two as
  directly comparable.
- All final review, editing, and submission of this report, the AI use
  disclosure, and the accompanying notebook.

## Model

Claude Sonnet 4.5, via claude.ai, over multiple sessions during
September 2026.
