# Working Effectively with Legacy Code

### Chapter 1 — Changing Software

* There are 4 reasons to change software: adding a feature, fixing a bug, refactoring, and optimizing. Each should change only what it's supposed to and preserve everything else.
* Users depend on behavior. Never break it without knowing!
* Before any change, answer: what breaks? how do I verify? how do I know nothing else broke? If you can't answer all 3, you're not ready to change yet.
* Avoiding change in legacy code makes it grow bigger, harder to understand, and scarier to touch over time.
* Tests are what make software change safe and give confidence to modify code without breaking the system.
* Most software changes modify a small part of the system while trying to preserve the rest of the existing behavior.

<img width="380" height="125" alt="01fig01" src="https://github.com/user-attachments/assets/a2244d9c-f83c-46d7-8a6f-3699c67ec236" />

---

### Chapter 2 — Working with Feedback

* **Stop "Edit and Pray":** Never change code and just hope you didn't break anything. Use tests as a software vise to lock existing behavior in place before you touch a single line.
* **Minutes, Not Overnight:** Overnight feedback loops kill productivity. You need localized tests that tell you if you made a mistake in seconds, not the next morning.
* **The Strict Unit Test Rule:** If a test touches a database, network, file system, or config files, it is not a unit test. True unit tests must run instantly.
* **The 0.01-Second Standard:** A test that takes 0.1 seconds is too slow. Aim for 1/100th of a second per test so running thousands of them takes minutes, not hours, keeping your daily momentum alive.
* **The Legacy Dilemma:** You need tests to change code, but you must change code to add tests. Accept temporary design "scars" (messy code modifications) just to get the class under test.
* **The 5-Step Change Algorithm:** When working on legacy code, always follow this strict sequence:
  1. *Identify change points:* Find exactly where the feature or fix needs to live.
  2. *Find test points:* Figure out where you can instantiate the class to watch the code execute.
  3. *Break dependencies:* Uncouple the target code from databases or heavy objects so it runs instantly.
  4. *Write tests:* Lock down the current behavior before you touch a single line of production logic.
  5. *Change & Refactor:* Ship the new feature, then clean up the design while your tests protect you.
* **Grow Islands of Safety:** You cannot fix a massive codebase all at once. Write localized tests to create safe "islands" of code during your daily tasks until they merge into safe continents.
