# Working Effectively with Legacy Code
## Part I : The Mechanics of Change
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
---

### Chapter 3 — Sensing and Separation


* Breaking dependencies serves two purposes: **Sensing** (inspecting internal values or side effects we can't otherwise access) and **Separation** (detaching code from its live environment so it can run in a test harness). You almost always need both.
* You cannot test or sense code if external API or hardware calls are buried deep within a class. In this initial design, the `Sale` class is tightly coupled to the buried display API, preventing sensing and separation.
    <img width="1336" alt="Screenshot 2026-06-19 173135" src="https://github.com/user-attachments/assets/57a70e64-0cf5-4a7e-8026-7b55e0a20d6c" />

* To begin breaking the dependency, extract the buried API code into a dedicated collaborator class. Here, the display logic is moved to `ArtR56Display`, separating the `Sale` class from the environment details, but sensing is still difficult because they are concrete classes.

<img width="1718"  alt="Screenshot 2026-06-19 173141" src="https://github.com/user-attachments/assets/67640250-5b26-4e92-abd3-7a213ee97b05" />

* To achieve sensing, introduce an interface for the collaborator. This allows you to swap the real class with a **Fake Object** during testing. By refactoring to the `Display` interface, the `Sale` class is fully decoupled from the real hardware.
<img width="1721"  alt="Screenshot 2026-06-19 173148" src="https://github.com/user-attachments/assets/a8eafff0-bdc0-4b64-b847-6cde07fc1221" />

* A fake object stands in for a real collaborator and has two "sides": it implements the production interface to trick the class under test, but exposes extra testing hooks (like `getLastLine()`) to let the unit test verify what happened.
* **"That's not really testing" is wrong.** A fake-based test won't catch a hardware bug, but it proves your logic sends the right data. Divide and conquer: test each unit in isolation, then localize bugs faster.
* When simple fakes are not enough, use **Mock Objects**. Mocks perform assertions internally by defining expectations (`setExpectation`) before running the code and verifying them afterward (`verify`). Use mocks when you'd otherwise need many throwaway fake classes.

### Chapter 4 — The Seam Model

* Stop seeing code as a "sheet of text" that you edit directly. Start looking for **seams** — places where you can swap behavior without touching that exact line of code.
* A **Seam** is a place where you can alter behavior in your program **without editing in that place**. Every seam has an **Enabling Point**: the place where you decide which behavior runs — test or production.
*  **Why seams matter:** when a method calls an external dependency (database, network, hardware), you cannot run it in a test. Instead of deleting or rewriting the call, you find a seam around it — so you can **neutralize** it during testing (e.g., override it with an empty method) or **replace** it with a fake that records what happened, all without editing the original method.

* Three types of seams:

  **1. Preprocessing Seam (C/C++ only)** — use `#define` to replace a function call before compilation. Enabling point = the `#ifdef TESTING` flag.

  **2. Link Seam** — swap a real library with a stub at build time. Enabling point = the makefile or build script. Always make the difference between test and production environments obvious.

  **3. Object Seam** — pass a different object (subclass or fake) to change which method runs. Enabling point = where you decide which object to create or inject.

* Not every method call is an object seam. If the object is created inside the same method, there is no enabling point — you cannot change which method runs without editing the code. If the object is passed in from outside, it is a seam.
* **Object seams are the best choice in OOP.** Use link or preprocessing seams only when dependencies are pervasive and nothing else works.
* Before touching any legacy method with external dependencies, find the seam first. Make the dependency virtual or injectable, write the test, then change safely.

> **The same dependency can expose multiple seams at once:**
>
> ```cpp
> bool CAsyncSslRec::Init() {
>     ...
>     if (!m_bFailureSent) {
>         m_bFailureSent = TRUE;
>         PostReceiveError(SOCKETCALLBACK, SSL_FAILURE); // 3 seams here
>     }
>     ...
> }
> ```
>
> 1. **Link Seam** — stub library. Enabling point = `makefile`.
> 2. **Preprocessing Seam** — `#define`. Enabling point = `#ifdef TESTING`.
> 3. **Object Seam** — `virtual` + override in test subclass. Enabling point = which object you create.

### Chapter 5 — Tools

* Mostly tool-specific guidance (xUnit, FIT, Fitnesse) from 2004. The only lasting takeaway: **automated refactoring tools can silently break behavior** — always have tests before you trust them.
