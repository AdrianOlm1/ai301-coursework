# Voice guide: how I talk upstream

## Who I am in threads

I'm a student making my first contributions to Path Review, strongest
in Python and new to this codebase. In a thread I report what I ran and
what I saw, say plainly what I haven't done yet, and ask when I'm
unsure where something belongs. Readers can expect exact commands and
output from me, not opinions about the code.

## Rules I write by

### Rule: Promise the next step, not the outcome

I only promise work I control: investigating, reproducing, and posting
what I find. Never a fix, a merge, or a date.

- Wrong: "I'll have this fixed and merged by Friday."
- Right: "Next I'll run the H-05 test with `--runxfail` and post the output here, whether or not it reproduces."

### Rule: Name this issue, not issues in general

Every comment names something only this issue has: the function, the
exact symptom, or the test. If the sentence would fit on any issue,
rewrite it.

- Wrong: "Hi, I'd love to work on this, please assign it to me!"
- Right: "I'd like to work on #72: `verify_password()` in `core/security.py` lets passlib's `UnknownHashError` escape instead of returning `False`."

### Rule: Claim only what my output shows

Certainty words ("confirmed", "root cause", "always") need an artifact
under them. A guess is labelled as a guess.

- Wrong: "The root cause is definitely that passlib can't identify the hash, so any bad input breaks login."
- Right: "For `\"not_a_valid_bcrypt_hash\"` the login route returned 500 in my run (output below). I haven't tested other callers."

### Rule: Add my own evidence, never "same here"

On a shared issue I post my own environment and output, and I say what
my report adds or where it differs from earlier ones instead of
repeating them.

- Wrong: "Same as above, can confirm on my machine."
- Right: "Earlier reports are from arm64 macOS, Windows and WSL; mine is Intel macOS on Python 3.12, and it also shows what `/auth/login` returns."

### Rule: Say how AI helped

When an AI assistant helped me run steps or draft the text, I say so in
one plain sentence, even when the repo doesn't require it.

- Wrong: (no mention, after Claude Code drafted the comment)
- Right: "I used Claude Code to help run these steps and draft this comment; I ran the commands myself and the output is copied from those runs."

## Things I never post

- A fix date, a "guaranteed", or "should be quick".
- "Please assign this to me" or "reserve this for me" with nothing else.
- "+1", "same here", or "can confirm" without my own output.
- A root-cause claim I haven't shown with output.
- Output I didn't actually get, or trimmed output that hides a difference.
