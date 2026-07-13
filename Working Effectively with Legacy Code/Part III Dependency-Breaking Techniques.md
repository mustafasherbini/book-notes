## Part III: Dependency-Breaking Techniques

### Chapter 25 — Dependency-Breaking Techniques

* This chapter is a toolbox of techniques for breaking dependencies in legacy code. Each solves a specific problem that prevents testing. The goal is never perfect design — it's **breaking the dependency with the smallest safe change** to get tests in place.

---

### Adapt Parameter

* **The Problem:** A method takes a parameter whose type is a large class or interface **you don't own** — it has dozens of methods, needs servers or hardware to instantiate, and you only need one or two of its methods.

* **The Solution:**
  1. Create a new small interface with **only the methods you need**.
  2. Create a **production implementation** that wraps the original object and delegates to it.
  3. Create a **fake implementation** that returns values you control.
  4. Change the method to accept the **new interface** instead of the original object.
  5. Write a test using the fake.

> ```csharp
> // Before — locked to a massive external interface (23 methods)
> public void Populate(HttpServletRequest request) { ... }
>
> // After — depends on a tiny interface you own (1 method)
> public void Populate(IParameterSource source) { ... }
> ```

* **Adapt Parameter vs. Extract Interface:** Always try **Extract Interface** first — it works when the class is **yours** and you can make it implement a new interface. Use **Adapt Parameter** only when the class is **not yours** or is an external interface you can't modify.

* **Why not a wrapper method that takes the raw value?** Works great for a single simple value. Falls apart when you need **multiple values**, have **back-and-forth interaction**, or need to **verify the method performed an action** (e.g., sent a message).

* **Why not subclass the original?** Its constructor usually requires complex dependencies (server, network) — you can't instantiate it in a test.

* **Why not implement the large interface directly?** You'd have to write every method — 22 empty stubs for the 1 you actually need.

* **What am I testing?** Not the source or the original object — you're testing **what your method does with the value once it arrives**. The fake controls inputs; you observe outputs.

* **Warnings:** This technique **changes the method signature** — all calling code must update. If the new interface is too different from the original, you risk **subtle bugs**. Safety first, beautiful design later — after tests are in place.

* **Hidden benefit:** Creates an **isolation layer** — if the data source changes tomorrow, your method stays untouched. Only the production implementation changes.
---

### Break Out Method Object

* **The Problem:** A **long method** inside a class that's **hard to instantiate** in a test harness. It uses instance data and methods from the class, so you can't extract it as a static method.

* **The Solution:**
  1. Create a **new class** — the "method object".
  2. Constructor takes a **reference to the original class** + the **same parameters** as the original method.
  3. Turn each parameter into an **instance variable**.
  4. Create an execution method (`Run()` / `Draw()`) and **copy the method body** into it.
  5. **Lean on the Compiler** — make needed methods/variables **public** or add **getters**.
  6. Make the original method **delegate** to the new class.
  7. If needed → **Extract Interface** to break the dependency on the original class.

> ```csharp
> // Original method just delegates now
> public void Draw(List<Point> roots, ColorMatrix colors, List<Point> selection)
> {
>     var renderer = new Renderer(this, roots, colors, selection);
>     renderer.Draw();
> }
> ```

* The interface should contain **only the methods the new class actually needs**. If the original has 20 methods and you only need `DrawPoint` — the interface has **one method**.

<img width="550" height="274" alt="25fig01" src="https://github.com/user-attachments/assets/33c0e25e-9da5-4a9c-9720-bc245013bbd5" />

* **Three variations:**

  | Case | What to do |
  |---|---|
  | Method uses **nothing** from the original class | Simplest — no reference needed |
  | Method uses **only data** | Put data in a new class, pass as argument |
  | Method uses **methods** from the original class | Hardest — need Extract Interface |
> **My opinion (Mustafa):** The first two variations don't really need a whole new class. Just add a wrapper method in the same class that takes the data as parameters and test it directly. 

* **"We made private things public!"** — Yes, it feels wrong. But it's the **first step** to get tests in place. Once tests exist, refactor safely.

* **This is not the final design.** Over time, break more dependencies → rethink the interface → maybe it becomes a real class. The concept of "Renderer" may evolve from a testing trick into a **real architectural pattern**.
