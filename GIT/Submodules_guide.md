---

**Git Submodules**.
They allow a “parent” repository to **reference other repositories**, displaying them as **folders**, and in the **GitHub UI** those folders appear as **clickable links** that redirect to the child repository.

Below, I explain the concept **first**, and then provide the **step-by-step procedure**, already aligned with usage via **CLI and VS Code**.

---

## Correct concept (important to understand)

* The **parent repository DOES NOT contain the code** of the children
* It contains **pointers (commit SHAs)** to each child repository
* Each child folder, in the GitHub UI, appears as:

  * A special icon
  * Text like:
    `repo-name @ a1b2c3d`
  * Click → redirects to the child repository

This is **the desired behavior** for your scenario.

---

## Final expected structure

```
parent-repo/
├── README.md
├── service-a/   → link to repo service-a
├── service-b/   → link to repo service-b
└── service-c/   → link to repo service-c
```

On GitHub:

* `service-a`, `service-b`, `service-c` are **links**
* They are not normal directories

---

## Step-by-step — configuring Submodules

### 1. Clone the parent repository (if you haven’t already)

```bash
git clone git@github.com:YOUR_USER/parent-repo.git
cd parent-repo
```

---

### 2. Add each child repository as a submodule

Syntax:

```bash
git submodule add <SSH_URL_OF_CHILD_REPO> <DESTINATION_FOLDER>
```

Real example:

```bash
git submodule add git@github.com:YOUR_USER/service-a.git service-a
git submodule add git@github.com:YOUR_USER/service-b.git service-b
git submodule add git@github.com:YOUR_USER/service-c.git service-c
```

Immediate result:

* The folders appear
* A **.gitmodules** file is created

---

### 3. Verify the `.gitmodules` file

It should look like this:

```ini
[submodule "service-a"]
  path = service-a
  url = git@github.com:YOUR_USER/service-a.git

[submodule "service-b"]
  path = service-b
  url = git@github.com:YOUR_USER/service-b.git

[submodule "service-c"]
  path = service-c
  url = git@github.com:YOUR_USER/service-c.git
```

This file **must be versioned**.

---

### 4. Commit in the parent repository

```bash
git status
```

You will see:

* `.gitmodules`
* `service-a`
* `service-b`
* `service-c`

Now:

```bash
git add .gitmodules service-a service-b service-c
git commit -m "Add submodules: service-a, service-b, service-c"
git push origin main
```

---

## Result in the GitHub UI

In the parent repository:

* The child folders appear
* Each one is a **link**
* Click → redirects to the corresponding repository
* Shows the pinned commit

✔ Exactly the behavior you want

---

## Day-to-day work (very important)

### Cloning the parent repository with submodules

Always use:

```bash
git clone --recurse-submodules git@github.com:YOUR_USER/parent-repo.git
```

Or, if you already cloned without this:

```bash
git submodule update --init --recursive
```

---

### Updating a submodule (e.g., service-a)

```bash
cd service-a
git pull origin main
cd ..
git add service-a
git commit -m "Update service-a submodule"
git push
```

> Note: the **parent only records that the child moved to a new commit**

---

## Using VS Code

* VS Code recognizes submodules automatically
* Each submodule works as an independent Git repository
* Source Control shows:

  * parent-repo
  * child-repo (when you enter the folder)

Normal workflow:

* Commit inside the child repository
* Then commit in the parent repository to update the pointer

---

## What NOT to use (common pitfalls)

❌ Manually copying code
❌ Git subtree (does not create a link in the GitHub UI)
❌ Monorepo (does not redirect to separate repositories)

Only **submodules** meet your exact visual and functional requirement.

---

## Alternatives (if requirements change)

| Option    | Redirects on GitHub | Code duplicated | Recommended |
| --------- | ------------------- | --------------- | ----------- |
| Submodule | Yes                 | No              | ✔           |
| Subtree   | No                  | Yes             | ❌           |
| Monorepo  | No                  | N/A             | ❌           |

---

Just say how you would like to proceed.
