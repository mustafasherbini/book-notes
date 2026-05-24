# Working Effectively with Legacy Code

### Chapter 1 — Changing Software
- There are 4 reasons to change software: adding a feature, fixing a bug, refactoring, and optimizing. Each should change only what it's supposed to and preserve everything else.
- Users depend on behavior. Never break it without knowing!
- Before any change, answer: what breaks? how do I verify? how do I know nothing else broke? If you can't answer all 3, you're not ready to change yet.
- Avoiding change in legacy code makes it grow bigger, harder to understand, and scarier to touch over time.
- Tests are what make software change safe and give confidence to modify code without breaking the system.
- Most software changes modify a small part of the system while trying to preserve the rest of the existing behavior.
        <img width="380" height="125" alt="01fig01" src="https://github.com/user-attachments/assets/a2244d9c-f83c-46d7-8a6f-3699c67ec236" />


