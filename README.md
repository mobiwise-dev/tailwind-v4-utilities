# Tailwind CSS v4 – Custom Utility Classes with `@utility` and `--value()`

Tailwind CSS v4 introduces a powerful feature: the ability to define your own utility classes directly in CSS using the `@utility` directive. Combined with `--value()` placeholders, this unlocks advanced, dynamic class patterns — all without JavaScript or `tailwind.config.js`.

This README explains how `@utility` works, how to use `--value(integer)` and `--value(--theme-key-*)`, and provides working examples including an innovative color utility pattern.

---

## 🔧 What is `@utility`?

The `@utility` directive allows you to define custom CSS classes as part of Tailwind’s generation process.

There are two main types of utility classes you can define:

### 1. **Static utility**
```css
@utility card {
  @apply bg-white p-4 rounded shadow;
}
```

Generates a single class `.card`.

---

### 2. **Dynamic utility with a wildcard (`*`)**
```css
@utility tab-* {
  tab-size: --value(integer);
}
```

Generates:
- `.tab-2` → `tab-size: 2;`
- `.tab-4` → `tab-size: 4;`

The `*` acts as a placeholder for a dynamic segment, and `--value(integer)` extracts it as a raw number.

---

## 🔢 Using `--value(integer)`

When you define a class like `mt-*`, and use `--value(integer)`:

```css
@utility mt-* {
  margin-top: calc(0.25rem * --value(integer));
}
```

Then:
```html
<div class="mt-4"></div>
```

...generates:
```css
.mt-4 {
  margin-top: calc(0.25rem * 4);
}
```

This is useful for spacing, sizes, grid values, etc.

---

## 🎨 Dynamic Colors with `--value(--color-*)`

For cases where you want to dynamically generate utility classes that map to **CSS variables** (like theme colors), you can combine `--value()` with a variable prefix.

```css
@theme {
  --color-primary: oklch(65% 0.25 270);
  --color-accent: oklch(75% 0.22 320);
}
```

Then define a dynamic utility:

```css
@utility custom-link-* {
  color: --value(--color-*);
}
```

Which interprets:
```html
<a class="custom-link-primary">Primary</a>
<a class="custom-link-accent">Accent</a>
```

...as:
```css
.custom-link-primary {
  color: var(--color-primary);
}

.custom-link-accent {
  color: var(--color-accent);
}
```

### ✅ This syntax:
```css
color: --value(--color-*);
```
...is **valid and works**, even though it’s **not yet documented** in the official Tailwind docs. You're using the value in the class name (`primary`, `accent`, etc.) to reference a CSS variable `--color-*`.

---

## 🧪 Full Example

```css
@import "tailwindcss";

@theme {
  --color-primary: oklch(65% 0.25 270);
  --color-accent: oklch(75% 0.22 320);
}

@utility custom-link-* {
  position: relative;
  color: --value(--color-*);
  font-weight: 600;

  &:hover {
    text-decoration: none;
  }

  &::before,
  &::after {
    position: absolute;
    content: "";
    width: 0;
    transition: width 0.2s;
  }

  &::before {
    bottom: -0.25rem;
    left: 45%;
    border-top: 2px solid --value(--color-*);
  }

  &::after {
    bottom: -0.25rem;
    right: 45%;
    border-bottom: 2px solid --value(--color-*);
  }

  &:hover::before,
  &:hover::after {
    width: 55%;
  }
}
```

---

## 📝 Notes

- Always define pseudo-classes and pseudo-elements (`:hover`, `::before`, etc.) **inside the `@utility` block**. Tailwind does not support them directly in the utility name.
- `--value(--color-*)` is a clever, working hack that mimics `var(--color-primary)` based on class name.
- `--value(integer)` is only for numeric input.
- This system keeps your CSS highly composable and scalable — perfect for design systems and tokens.

---

## 🏁 Conclusion

Tailwind CSS v4's `@utility` and `--value()` system allows fine-grained, semantic, and dynamic class creation — all from pure CSS.

Whether you're building token-based design systems or want full control over generated classes, this approach gives you powerful tools without relying on JS configuration.

---

Happy styling! 🎉
