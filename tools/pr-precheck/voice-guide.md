# Voice guide: how I talk upstream

## Who I am in threads

I am a student contributor who has worked on Python and FastAPI projects, including PathReview. I will describe what I actually tested and share the commands and output so someone else can repeat the attempt. I will keep the claim modest until I have evidence.

## Rules I write by

### Rule: Name this issue

Say which behavior I will investigate, rather than asking vaguely to be assigned.

- Wrong: "Can I work on this?"
- Right: "I will investigate why `verify_password` raises on a malformed stored hash and report the test result here."

### Rule: Promise the investigation, not the fix

Before reproducing, do not claim I have confirmed the bug or guarantee a delivery date.

- Wrong: "I found the cause and will fix this by tomorrow."
- Right: "I will run the existing test against the current code and post the observed output."

### Rule: Put the command next to its result

When I report a reproduction, give the exact trigger and a compact excerpt of the result.

- Wrong: "It fails on my machine too."
- Right: "I ran `pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -q`; the test raised `UnknownHashError` before returning `False`."

### Rule: Label a hypothesis

Keep a proposed cause separate from an observed result.

- Wrong: "Passlib is broken."
- Right: "The traceback reaches `pwd_context.verify`; I suspect the invalid stored hash is not caught, but I have not tested a fix."

## Things I never post

- A claim that I reproduced something before running it.
- A promised fix date or an unsupported root-cause statement.
- A copied classmate report or a “same here” reply without my own environment and evidence.
- An AI-assisted comment without disclosure if the repository explicitly requires it.
