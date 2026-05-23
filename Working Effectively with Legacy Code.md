# Working Effectively with Legacy Code

### Chapter 1 — Changing Software

- There are 4 reasons to change software: adding a feature, fixing a bug, refactoring, and optimizing — each should change only what it's supposed to and preserve everything else.
- Behavior is what users depend on — never break it without knowing.
- Before any change, answer: what breaks? how do I verify? how do I know nothing else broke? If you can't answer all 3 → you're not ready to change yet.
- Avoiding change makes code grow bigger and harder — fear increases every day.
- Tests are the only way to change safely — not more people, not more thinking.
- Fear of touching code = sign that tests are missing.
- Preserving existing behavior is the biggest challenge — you change a small thing but must preserve everything else, and you often don't know how much is at risk.
