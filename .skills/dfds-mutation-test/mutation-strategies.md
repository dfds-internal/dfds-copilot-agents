# Mutation Strategies Reference

Strategies for generating semantically meaningful mutations. Each category includes examples showing weak tests that would miss the mutation vs. strong tests that catch it.

## 1. Conditional Boundary Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `x > 0` | `x >= 0` | Off-by-one at zero boundary |
| `x <= limit` | `x < limit` | Excludes boundary value |
| `err != null` | `err == null` | Inverted error/null handling |

```
// age >= 18
// Mutant: age > 18

WEAK:  isAdult(25) → true both ways (25 >= 18 and 25 > 18)
STRONG: isAdult(18) → true vs false (18 >= 18 but NOT 18 > 18)
```

## 2. Logical Operator Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `a && b` | `a \|\| b` | Wrong logical operator |
| `a \|\| b` | `a && b` | Wrong logical operator |
| `!condition` | `condition` | Removed negation |

```
// isAdmin || isOwner
// Mutant: isAdmin && isOwner

WEAK:  canAccess(true, true) → true both ways
STRONG: canAccess(true, false) → true vs false
```

## 3. Arithmetic Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `a + b` | `a - b` | Wrong operator |
| `a * b` | `a / b` | Inverted calculation |
| `count + 1` | `count` | Missing increment |
| `index - 1` | `index` | Off-by-one |

```
// price * quantity
// Mutant: price / quantity

WEAK:  calculate(10, 1) → 10 both ways (10*1 = 10/1)
STRONG: calculate(10, 3) → 30 vs 3.33
```

## 4. Return Value Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `return result` | `return null` / `return ""` / `return None` | Missing return value |
| `return true` | `return false` | Wrong boolean |
| `return items` | `return null` / `return []` / `return new List<T>()` | Empty result |

**Language-specific patterns:**

| Language | Original | Mutation | Bug Simulated |
|----------|----------|----------|---------------|
| C# | `return Result.Ok(value)` | `return Result.Fail(...)` | Fabricated failure |
| C# | `return value` | `return default` | Default value instead of computed |
| Python | `return result` | `return None` | Missing return |
| Python | `raise ValueError(...)` | `pass` | Swallowed exception |
| TypeScript | `return result` | `return undefined` | Missing return |
| Go | `return result, nil` | `return result, errors.New(...)` | Fabricated error |

## 5. Guard / Early Return Removal

Remove entire guard clauses.

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `if (err != null) { return err; }` | *(remove block)* | Silently swallowed error |
| `if (!authorized) { return 403; }` | *(remove block)* | Missing authorization |
| `if (input == "") { return default; }` | *(remove block)* | Missing validation |

**Language-specific examples:**

| Language | Original | Mutation |
|----------|----------|----------|
| C# | `if (x is null) throw new ArgumentNullException(...)` | *(remove block)* |
| C# | `Guard.Against.Null(x)` | *(remove line)* |
| Python | `if not x: raise ValueError(...)` | *(remove block)* |
| TypeScript | `if (!x) throw new Error(...)` | *(remove block)* |
| Go | `if err != nil { return err }` | *(remove block)* |

```
// processOrder: validateOrder(order); saveOrder(order); sendConfirmation(order);
// Mutant: empty function body

WEAK:  expect(() => processOrder(order)).not.toThrow()  // empty fn also doesn't throw
STRONG: processOrder(order); expect(db.save).toHaveBeenCalledWith(order)
```

## 6. Collection / Loop Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| Loop starts at `0` | Loop starts at `1` | Skips first element |
| Add item to collection | *(remove)* | Lost data |
| Access last element | Access first element | Wrong element |
| Sort collection | *(remove)* | Missing ordering |

**Language-specific examples:**

| Language | Original | Mutation |
|----------|----------|----------|
| C# | `list.Add(item)` | *(remove line)* |
| C# | `items.OrderBy(x => x.Name)` | `items` |
| C# | `items.Where(x => x.IsActive)` | `items` |
| Python | `items.append(item)` | *(remove line)* |
| Python | `sorted(items, key=...)` | `items` |
| Python | `[x for x in items if condition]` | `items` |
| TypeScript | `items.push(item)` | *(remove line)* |
| TypeScript | `items.filter(...)` | `items` |
| Go | `append(slice, item)` | `slice` |

## 7. Method / Function Call Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `startsWith()` / `.StartsWith()` / `.startswith()` | `endsWith()` / `.EndsWith()` / `.endswith()` | Wrong string position |
| `toUpperCase()` / `.ToUpper()` / `.upper()` | `toLowerCase()` / `.ToLower()` / `.lower()` | Wrong case |
| `some()` / `.Any()` / `any()` | `every()` / `.All()` / `all()` | Partial vs full match |
| `Math.min()` / `Math.Min()` / `min()` | `Math.max()` / `Math.Max()` / `max()` | Wrong extremum |
| `trim()` / `.Trim()` / `.strip()` | `trimStart()` / `.TrimStart()` / `.lstrip()` | Incomplete trim |

**C#-specific LINQ mutations:**

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `.First()` | `.Last()` | Wrong element |
| `.FirstOrDefault()` | `.First()` | Missing null safety |
| `.Single()` | `.First()` | Missing uniqueness check |
| `.Count()` | `0` / `1` | Wrong count |
| `.Any()` | `true` / `false` | Skipped check |
| `.Select(...)` | *(remove)* | Missing projection |
| `.Distinct()` | *(remove)* | Duplicate values |

## 8. Null Safety / Optional Mutations

| Language | Original | Mutation | Bug Simulated |
|----------|----------|----------|---------------|
| TypeScript | `foo?.bar` | `foo.bar` | Missing null guard |
| TypeScript | `x ?? fallback` | `x` | Missing fallback |
| C# | `foo?.Bar` | `foo.Bar` | Missing null guard |
| C# | `x ?? fallback` | `x` | Missing fallback |
| C# | `x!` | `x` | Null forgiveness removal |
| Python | `x if x is not None else fallback` | `x` | Missing fallback |
| Python | `x or default` | `x` | Missing fallback |
| Go | `if x != nil` | `if x == nil` | Nil dereference |

## 9. Unary Operator Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `+a` | `-a` | Wrong sign |
| `-a` | `+a` | Wrong sign |
| `++a` / `a++` | `--a` / `a--` | Increment vs decrement |

## 10. String / Constant Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `"text"` | `""` | Empty string |
| `""` | `"mutated"` | Non-empty where empty expected |

**Language-specific status/constant mutations:**

| Language | Original | Mutation |
|----------|----------|----------|
| C# | `HttpStatusCode.OK` | `HttpStatusCode.NotFound` |
| C# | `StatusCodes.Status200OK` | `StatusCodes.Status404NotFound` |
| TypeScript | `200` (status) | `404` |
| Python | `status.HTTP_200_OK` | `status.HTTP_404_NOT_FOUND` |
| Go | `http.StatusOK` | `http.StatusNotFound` |

## 11. State Transition & Domain Mutations

| Original | Mutation | Bug Simulated |
|----------|----------|---------------|
| `status = Active` | `status = Inactive` | Wrong state |
| Apply domain event | *(remove)* | Event not applied |
| Append to event list | *(remove)* | Lost domain event |
| Field assignment in event handler | *(remove)* | State not updated from event |
| Version increment | *(remove)* | Broken concurrency check |

## 12. Exception / Error Handling Mutations

| Language | Original | Mutation | Bug Simulated |
|----------|----------|----------|---------------|
| C# | `throw new InvalidOperationException(...)` | *(remove)* | Missing error |
| C# | `catch (SpecificException)` | `catch (Exception)` | Over-broad catch |
| Python | `raise ValueError(...)` | `pass` | Swallowed exception |
| Python | `except SpecificError:` | `except Exception:` | Over-broad catch |
| TypeScript | `throw new Error(...)` | *(remove)* | Missing error |
| Go | `return fmt.Errorf(...)` | `return nil` | Swallowed error |

## 13. Async / Concurrency Mutations

| Language | Original | Mutation | Bug Simulated |
|----------|----------|----------|---------------|
| C# | `await task` | `task` (fire-and-forget) | Missing await |
| C# | `ConfigureAwait(false)` | *(remove)* | Deadlock risk |
| Python | `await coroutine` | `coroutine` | Missing await |
| TypeScript | `await promise` | `promise` | Missing await |

---

## Equivalent Mutants

Equivalent mutants produce identical behavior to the original — they cannot be killed and should be skipped.

**Common patterns:**
- Operations with identity elements: `+= 0`, `-= 0`, `*= 1`, `/= 1`
- Boundary conditions where both sides have the same outcome
- Mutations in dead code paths that are never reached
- Duplicate conditions where another check already covers the case

When you identify an equivalent mutant, skip it and note it in the report.

## Identity Values to Avoid in Tests

Tests using these values cannot distinguish between mutated operators:

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| `0` for `+` / `-` | `x + 0 = x - 0` | Non-zero: `3`, `7` |
| `1` for `*` / `/` | `x * 1 = x / 1` | Values > 1: `3`, `5` |
| `""` for string ops | Empty string edge cases | `"abc"`, `"test"` |
| `true, true` for `&&`/`\|\|` | `T && T = T \|\| T` | `true, false` |
| `false, false` for `&&`/`\|\|` | `F && F = F \|\| F` | `true, false` |
| `[]` for array ops | Empty array edge cases | `[1, 2, 3]` |
| All-matching arrays for `some`/`every` | `some(allTrue) = every(allTrue)` | Partially matching arrays |
