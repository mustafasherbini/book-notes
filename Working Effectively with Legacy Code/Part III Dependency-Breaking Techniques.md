# Working Effectively with Legacy Code

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