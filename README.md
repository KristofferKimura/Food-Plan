# Family Food Plan

A single-file web app (`index.html`). No server, no database, no build step.

- Store dishes with just a name and a "what to buy" list
- Generate a random Monday–Sunday plan (no repeats while you have enough dishes)
- Lock days you like and re-roll the rest, or pick a dish manually
- Drag the ⠿ handle to swap two days (mouse or touch)
- Tick "Made" on a day and say who cooked it (Mom / Dad); the last 4 weeks are listed under the week, and the generator avoids dishes made in the last 7 days
- Undo button (top right) and a toast after most changes
- Shopping list grouped by store section (fruit & veg, meat, dairy…), with a box to add your own items, and a visible "Always at home" list
- Haruki tab (placeholder for now)
- Dark theme
- Export / import a JSON backup

The saved data has a version number (`v`), so the page can be upgraded later without breaking data you already entered. If you open an older cached copy of the page after the data was upgraded, it will stop writing and ask you to reload.

## Important: where is the data stored?

In each browser's `localStorage`. That means every phone/computer has its **own** copy.
To share dishes between family members: Dishes tab → **Export**, then **Import** on the other device.

To share one live plan between you and your partner, connect the free shared database (see "Shared sync" below). Without it, the app works fine on a single device.

## Food types, weekend tag and filters

Each dish has a **food type** (chicken, beef, pork, fish/seafood, vegetarian, other) and an optional **🎉 weekend dish** tag.
Under the Generate button, the **Filters** panel controls the random plan:

- **Max of the same type per week** (default 2), so no 3 chicken dinners
- **Never the same type on back-to-back days** (when possible)
- **Fish/seafood at least N per week** (default 1)
- **Weekend dishes only on Sat/Sun**, and Sat/Sun prefer weekend dishes
- **Include these food types**, e.g. untick Pork for a week
- "Other" (pasta, pizza, soup) is exempt from the type limits
- Locked days (🔒) always stay

If your dishes can't satisfy all rules (e.g. you have no fish dish), the app relaxes one rule at a time and tells you which.
Filter settings are shared through sync, same as the dishes.

## Free hosting options

### 1. Run it at home (easiest, only works on your home Wi-Fi)
Put `index.html` in a folder and run one of:

    python3 -m http.server 8080

Then open `http://<your-computer-ip>:8080` on any device on the same network.
A Raspberry Pi or old laptop that stays on works well for this.

### 2. GitHub Pages (free, always online)
1. Create a GitHub account and a new public repository, e.g. `food-plan`.
2. Upload `index.html` to it.
3. Settings → Pages → Source: "Deploy from a branch" → `main` / root → Save.
4. After a minute it is live at `https://<username>.github.io/food-plan/`.

### 3. Netlify Drop or Cloudflare Pages (free, no git needed)
- Netlify: go to https://app.netlify.com/drop and drag the folder containing `index.html` onto the page. You get a URL immediately.
- Cloudflare Pages: Workers & Pages → Create → Pages → Upload assets → drag the folder.

## Shared sync (one plan for the whole family)

Uses Firebase Realtime Database (free "Spark" plan, plenty for this). One-time setup, about 5 minutes:

1. Go to https://console.firebase.google.com and sign in with a Google account.
2. **Create a project** (name it anything, turn Google Analytics off).
3. In the left menu: **Build → Realtime Database → Create Database**. Pick a location (Belgium `europe-west1` is closest to Norway) and start in **test mode**.
4. Open the **Rules** tab and replace the content with the following, then **Publish**:

       {
         "rules": {
           ".read": true,
           ".write": true
         }
       }

   This matters: test mode expires after 30 days and the app would stop saving. These rules never expire. They also mean anyone who knows your database link can read and edit the plan, which is fine here since security isn't a concern.
5. On the **Data** tab, copy the database URL at the top. It looks like `https://yourproject-default-rtdb.europe-west1.firebasedatabase.app`.
6. Connect the app, either way:
   - **Per device:** open the app → Dishes tab → paste the link under "Shared sync" → **Save & connect**. Do this once on each phone/computer.
   - **Built in (easier for family):** open `index.html`, paste the link into `DEFAULT_SYNC_URL = "..."` near the bottom, and re-upload it to GitHub. Every device that opens the page is then connected automatically.

The top of the page shows "✓ Synced 20:05" when it works. Changes appear on the other device within about 5 seconds (or immediately when you reopen the app). If two people edit at the same moment, the last change wins.

## Tip
On a phone, open the page and choose "Add to Home Screen" so it behaves like an app.
