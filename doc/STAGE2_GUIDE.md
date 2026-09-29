# Stage 2 Submission Guide

> How to complete and submit **PT1-Stage 2: Conceptual and Logical Database Design**

---

## Step 1 — File to Submit

The main submission file is already created:

📄 **[`doc/stage2_erd.md`](./stage2_erd.md)**

This file contains all required content:
- ✅ ER Diagram (Mermaid format, renders on GitHub)
- ✅ Entity assumptions (8 entities, more than the required 5)
- ✅ Relationship descriptions with cardinality (8 relationships)
- ✅ At least 2 relationship types: **1:N** and **M:N**
- ✅ Normalization proof (BCNF for all 8 tables)
- ✅ Relational schema in the required format

---

## Step 2 — Check the Rubric

| Rubric Item | Status | Points |
|------------|--------|--------|
| File is in the `doc/` folder | ✅ Done | avoids -0.5% |
| Complete and correct ER/UML diagram | ✅ Done | +6% |
| 5 or more entities | ✅ 8 entities | +2% |
| Normalized (BCNF) with clear explanation | ✅ BCNF for all tables | +5% |
| Complete relational schema in correct format | ✅ All 8 tables | +7% |
| Assumptions for each entity | ✅ All 8 described | avoids -0.5% each |
| Cardinality description for each relationship | ✅ All 8 described | avoids -1% each |

---

## Step 3 — Push to GitHub

Open your terminal and run:

```bash
cd "/Users/michael/Documents/UIUC/CS 411"

git add doc/stage2_erd.md doc/erd_diagram/ erd_diagram/
git commit -m "Stage 2: Add ER diagram and relational schema"
git push origin main
```

---

## Step 4 — Create a GitHub Release

1. Go to your GitHub repo: `https://github.com/kangmoonsu/fa26-cs411-schema4`
2. Click **"Releases"** on the right sidebar (or go to the `Releases` tab)
3. Click **"Create a new release"** (or "Draft a new release")
4. Fill in the form:
   - **Tag version:** `stage.2`  ← must be exactly this
   - **Target branch:** `main`
   - **Release title:** `Stage 2 - Conceptual and Logical Database Design`
   - **Description:** (optional) "ER diagram, entity assumptions, cardinality, BCNF normalization, and relational schema."
5. Click **"Publish release"**

> ⚠️ The tag must be exactly `stage.2` — a wrong tag loses **-2%**

---

## Step 5 — Submit on Canvas

1. Go to Canvas → CS 411 → **PT1-Stage 2**
2. In the submission box, paste the link to your **GitHub Release**:
   ```
   https://github.com/kangmoonsu/fa26-cs411-schema4/releases/tag/stage.2
   ```
3. Submit.

---

## Optional: Stage 1 Revision (to recover lost points)

If your team lost points on Stage 1, you can revise it. To do that:

1. Fix the original Stage 1 documents inside your repo.
2. Create a new file:

   ```bash
   touch "/Users/michael/Documents/UIUC/CS 411/doc/stage1_revisions.md"
   ```

3. In `stage1_revisions.md`, explain:
   - What was wrong in Stage 1
   - What feedback you received
   - What you changed and why

4. Include everything in the same `stage.2` GitHub release.

---

## Summary Checklist

- [ ] `doc/stage2_erd.md` is committed and pushed to `main`
- [ ] GitHub Release created with tag exactly `stage.2`
- [ ] Submitted the release URL on Canvas
- [ ] (If applicable) `doc/stage1_revisions.md` added and included in release
