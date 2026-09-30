# Archit's Pokédex

A Gen 1 Pokédex built in React: browse the original 151 Pokémon, inspect stats and sprites, search by name or number, and tap a move to read its flavor text — all backed by [PokéAPI](https://pokeapi.co/).

---

## What it does

- **National Dex 001–151** in a filterable side nav
- **Detail card** with types, official artwork, extra sprites, and base stats
- **Move list** that opens a modal with the move name and in-game description
- **Responsive layout** — hamburger menu on small screens, sticky nav on desktop
- **Local cache** so repeat visits hit `localStorage` instead of the network

---

## Tech stack

| Layer | Choice |
| --- | --- |
| UI | React 19 |
| Bundler / dev server | Vite 8 |
| Styling | Tailwind CSS 4 (Vite plugin), custom CSS (`index.css`, `fanta.css`) |
| Icons | Font Awesome 6 |
| Data | [PokéAPI](https://pokeapi.co/docs/v2) (REST, no key) |
| Lint | Oxlint |

**PokéAPI endpoints used**

- `GET https://pokeapi.co/api/v2/pokemon/{id}` — species payload (types, stats, sprites, moves)
- Move resource URLs from that payload (`/api/v2/move/{id}`) — flavor text for the modal

Static Gen 1 portraits are served from `public/pokemon/` as `/pokemon/{dex}.png`. Extra sprites come from URLs on the Pokémon object.

---

## Concepts I used and learned

This project is a practical pass through core React and frontend data-flow, not a tutorial dump.

**Component architecture**  
Split the UI into `Header`, `SideNav`, `PokeCard`, `TypeCard`, and `Modal`. Parent `App` owns which Pokémon is selected and whether the mobile menu is open, then passes handlers and state down as props.

**State (`useState`)**  
Multiple independent slices: selected index, menu visibility, search string, fetched Pokémon JSON, loading flags, and the currently opened skill. That made it clear *who* owns *what*.

**Effects (`useEffect`)**  
Fetch (or hydrate from cache) whenever `selectedPokemon` changes. Learned to guard loading states so the card does not render `types` / `stats` / `moves` before data exists.

**Lists, keys, and derived data**  
`map` for nav items, type chips, sprites, stats, and moves. Search is a **derived list**: `filter` over the 151 names by dex number or name, then map the result.

**Controlled inputs**  
The search field is a controlled component (`value` + `onChange`) so the filtered list always matches what you type.

**Conditional rendering**  
Loading UI, selected nav highlight, and the skill modal only when `skill` is set.

**Async JavaScript**  
`async` / `await` + `fetch` + `try` / `catch` / `finally` for both Pokémon and move requests.

**Browser storage as a cache**  
Two `localStorage` keys: `pokedex` (full Pokémon payloads) and `pokemon-moves` (move name + description). Same Pokémon or move does not need a second network trip in that browser.

**Portals**  
`Modal` uses `ReactDOM.createPortal` into `#portal` so the overlay sits above the app tree (and CSS stacking) instead of being trapped inside the card.

**Composition**  
The modal takes `children`, so the card can pass in whatever skill UI it needs without baking copy into the portal component.

**Styling patterns**  
Utility + custom layout CSS, type colors from a lookup object applied as inline styles, and Font Awesome for the menu / back buttons.

**Vite mental model**  
ES modules, HMR, and static files from `public/` at the site root.

---

## Project layout

```
pokedex-app/
├── index.html              # root + portal mount, Font Awesome
├── public/pokemon/         # local dex portraits
├── src/
│   ├── App.jsx             # selected Pokémon + menu state
│   ├── main.jsx
│   ├── index.css
│   ├── fanta.css
│   ├── utils/              # 151 names, type colors, dex helpers
│   └── components/
│       ├── Header.jsx
│       ├── SideNav.jsx
│       ├── PokeCard.jsx
│       ├── TypeCard.jsx
│       └── Modal.jsx
└── package.json
```

---

## Run locally

```bash
cd pokedex-app
npm install
npm run dev
```

Open the URL Vite prints (usually `http://localhost:5173`).

| Script | Purpose |
| --- | --- |
| `npm run dev` | Dev server |
| `npm run build` | Production build |
| `npm run preview` | Serve the build locally |
| `npm run lint` | Oxlint |

---

## Credits

- Data: [PokéAPI](https://pokeapi.co/)
- Pokémon is a trademark of Nintendo / Game Freak / The Pokémon Company. This is a fan learning project, not an official product.
