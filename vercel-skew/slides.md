---
# try also 'default' to start simple
theme: the-unnamed
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: black
# some information about your slides, markdown enabled
title: Vercel Skew Protection
class: text-center
layout: center
info: |
  Introduction to Vercel Skew Protection
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: true
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# Vercel Skew Protection

Deep dive into skew protection 🤿

<div class="fixed left-0 bottom-0 p-4 justify-between w-full flex">
  <a href="https://nextjs-skew-demo.vercel.app/" target="_blank" class="text-xl">
    https://nextjs-skew-demo.vercel.app/
  </a>
  <a href="https://shellbear.me/" target="_blank" class="text-xl">
    https://shellbear.me
  </a>
</div>

---

# What is Skew Protection?

<div grid="~ cols-2 gap-4 items-center justify-center">
  <img src="/intro.png"/>
  <div class="flex flex-col gap-4">
    <div grid="~ cols-2 gap-4 items-center justify-center">
      <img src="/tweet-intro-one.png" >
      <img src="/tweet-intro-two.png" >
    </div>
    <img src="/version-skew-v2.svg" >
  </div>
</div>

<br />

Read more about: [https://vercel.com/blog/version-skew-protection](https://vercel.com/blog/version-skew-protection)

---
layout: iframe
url: https://nextjs-skew-demo.vercel.app/
---
---

# Making breaking changes

````md magic-move
```tsx
function CreatePostForm() {
  async function handleCreate(data: FormData) {
    "use server";

    // Fixing typo "tittle" -> "title"
    const title = data.get("tittle");
    if (typeof title !== "string" || !title) {
      throw new Error("Title is required");
    }

    // And fixing this SQL injection 💉
    await sql.query(`INSERT INTO posts (title) VALUES ('${title}')`);
    revalidatePath("/");
  }

  return (
    <form action={handleCreate}>
      <input name="tittle" placeholder="Add new task" required />
      <button type="submit">+</button>
    </form>
  );
}
```

```tsx
function CreatePostForm() {
  async function handleCreate(data: FormData) {
    "use server";

    // Fixing typo "tittle" -> "title"
    const title = data.get("title");
    if (typeof title !== "string" || !title) {
      throw new Error("Title is required");
    }

    // And fixing this SQL injection 💉
    await sql`INSERT INTO posts (title) VALUES (${title})`;
    revalidatePath("/");
  }

  return (
    <form action={handleCreate}>
      <input name="title" placeholder="Add new task" required />
      <button type="submit">+</button>
    </form>
  );
}
```
````

--- 

# And suddenly...

<video autoplay loop muted>
  <source src="/breaking-change.mov" type="video/mp4">
</video>

---
transition: fade 
---

# The error origin

![origin](/error-log.png)

---

# Enabling the feature

![enable](/enable.png)

---

# How it works? 🤔

![raw-http](/raw-http.png)

--- 

# Two concurrent versions 

<video autoplay loop muted>
  <source src="/two-versions.mov" type="video/mp4">
</video>

---
transition: fade 
---

# Target the chosen deployment 🎯

```yaml
headers:
  key: x-deployment-id
  value: DEPLOYMENT_ID

query-param:
  key: dpl
  value: DEPLOYMENT_ID

cookie:
  key: __vdpl
  value: DEPLOYMENT_ID
```

---

# Target the chosen deployment 🎯

<video autoplay loop muted>
  <source src="/exploit.mov" type="video/mp4">
</video>

---

# Mitigation (for each deployment ☠️)

<video autoplay loop muted>
  <source src="/mitigation.mov" type="video/mp4">
</video>

---
layout: two-cols-header
---

# Conclusion

::left::

- A "Protection" feature which introduces more security risks
- Manual action to revoke old deployments but breaks the point of the "skew" protection
- Increases cost since it creates more concurrent deployments $$$

::right::

<img src="/meme.png" alt="meme" width="400px" >