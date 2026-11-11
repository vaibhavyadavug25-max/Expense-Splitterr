# Expense Splitter

A lightweight web app that splits shared group expenses and works out the **fewest possible payments** needed for everyone to settle up.

**Author:** Vaibhav Yadav (Roll/Enrollment No. 2581175)
**Course:** Cyber Security: Project (VCS)

> 🔗 **Live demo:** `https://<your-username>.github.io/<repo-name>/` (after enabling GitHub Pages, see below)

---

## Problem Statement

When a group shares costs (a trip, a flat, a dinner), people pay for different things and everyone ends up owing everyone else a little. Settling each debt individually takes many transfers and causes confusion. Existing apps often need accounts, servers and permissions to store financial data about your friends.

## Objectives

1. Record who paid for what and who shared it (equal or exact split).
2. Compute each person's net balance accurately, with no rounding errors.
3. Minimize the number of settlement transactions using a debt-simplification algorithm.
4. Keep all data private: runs entirely in the browser with no backend.

## Features

- Add or remove people; add expenses with a payer and participants
- **Equal split** (leftover paise distributed fairly) or **exact amounts** with validation
- Live **net balance** table: who is owed and who owes
- **Debt minimization**: shows the simplified payment list and compares it with the un-optimized count
- Data persisted in `localStorage`; one-click demo data and reset
- Responsive layout with automatic dark mode
- Input escaping against HTML injection (`esc()` before rendering user text)

## Tech Stack

| Layer | Technology |
|---|---|
| Structure | HTML5 |
| Styling | CSS3 (custom properties, grid, `prefers-color-scheme`) |
| Logic | Vanilla JavaScript (ES6), no frameworks or libraries |
| Storage | Browser `localStorage` |
| Hosting | GitHub Pages (static) |

## Run Locally

No build step. Download the repo and open `index.html` in any modern browser.

## Deploy on GitHub Pages

1. Create a new GitHub repository and upload `index.html` and `README.md`.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`, then **Save**.
4. After a minute the site is live at `https://<your-username>.github.io/<repo-name>/`.

## How the Algorithm Works

**Step 1: Net balance.** For every person: `net = total paid − total share owed`. All money is stored as integers (paise/cents) to avoid floating-point errors. The sum of all net balances is always 0.

**Step 2: Split into two groups.** Net > 0 → *creditors* (should receive). Net < 0 → *debtors* (should pay).

**Step 3: Greedy matching.** Repeat:
1. Pick the debtor with the largest debt and the creditor with the largest credit.
2. The debtor pays `min(debt, credit)` to the creditor.
3. Reduce both; remove whoever reaches 0.

Each step fully settles at least one person, so for *n* people there are **at most n − 1 payments**.

### Worked example (demo data)

| Expense | Paid by | Split between | Amount |
|---|---|---|---|
| Hotel | Asha | All 4 | 8000 |
| Dinner | Ravi | Asha, Ravi, Meera | 2400 |
| Cab | Meera | All 4 | 1200 |
| Tickets | Kabir | Ravi, Meera, Kabir | 3000 |
| Snacks | Asha | Asha, Kabir | 600 |

Un-optimized, that is **11** separate debts. After minimization only **3** payments remain: Meera → Asha 2900, Ravi → Asha 1700, Kabir → Asha 600.

### Complexity

- Time: O(n²) as implemented (re-sorting each round on tiny *n*); O(n log n) with a heap.
- Space: O(n).
- Computing the **true** minimum number of transactions is **NP-hard** (it reduces to partitioning the balances into the maximum number of zero-sum subsets). The greedy method is a standard, fast heuristic that always settles correctly with ≤ n − 1 payments and is optimal in most practical cases.

## Testing

The core functions were checked with a randomized test of 2000 groups (2–8 people, random expenses and participants). In every case:
- each equal split sums exactly to the expense amount,
- applying the suggested payments brings every balance to exactly 0,
- the number of payments is ≤ n − 1.

## Project Structure

```
.
├── index.html   # UI + logic (single file)
└── README.md
```

Key functions in `index.html`: `splitEqually`, `computeBalances`, `minimizeTransactions`, `naiveCount`.

## Security & Privacy Notes

- No server, no network requests, no third-party scripts: nothing leaves the device.
- User-entered text is HTML-escaped before display to prevent XSS.
- Data is stored only in the browser's `localStorage`; clearing site data removes it.

## Future Scope

- Percentage and share-based splits; multi-currency support
- Export/import (CSV/JSON) and shareable links
- Optional cloud sync with end-to-end encryption
- Exact minimum-transaction solver for small groups (subset-sum DP)
- Receipt photo attachment and expense categories

## References

- Min Cash Flow problem (greedy settlement of debts), standard algorithms reference, e.g. GeeksforGeeks "Minimize Cash Flow among a given set of friends"
- MDN Web Docs: Web Storage API, CSS Grid, `prefers-color-scheme`

## License

MIT. Free to use for learning.
