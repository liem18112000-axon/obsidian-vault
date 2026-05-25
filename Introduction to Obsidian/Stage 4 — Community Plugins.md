# Overview
This is where Obsidian stops being a nice Markdown editor and becomes a personal system. Two things to know before the list:

**Where to find them:** **Settings → Community plugins → Turn on community plugins → Browse**. They install from within the app. Each is third-party code, so stick to popular, well-maintained ones (high download counts, recent updates).

**The landscape shifted recently.** The single biggest change: Obsidian shipped **Bases** as a _native_ feature, which now does much of what Dataview did — so the old "everyone installs Dataview first" advice no longer holds. I've reorganized Stage 4 around that.

# Bases — Native Databases (start here)

Bases turns any collection of notes into a database view, driven by their frontmatter properties. It launched as an official plugin and is already drawing migrations away from Dataview. [alternativeto](https://alternativeto.net/news/2025/8/obsidian-launches-new-bases-plugin-for-database-workflows-and-property-format-changes)
The reason it matters for you specifically: Dataview-to-Bases is like a manual stick-shift to an automatic — Bases gives you a visual interface to build filters and sorting, the tables render far faster, and they can be edited inline. The main reason to move from Dataview to Bases is speed. [Effortless Academic](https://effortlessacademic.com/using-obsidian-bases-for-academic-note-taking/)[Practical PKM](https://practicalpkm.com/moving-to-obsidian-bases-from-dataview/)

Because it's native, it's technically a _core_ plugin now — but conceptually it belongs here with the querying tools. For a scrum/daily-note workflow, a Base can roll up "all `#scrum` notes this sprint" into a sortable table with zero code. Start with this before reaching for Dataview.

# Dataview — Still Relevant for the Hard Cases

Dataview is the original query engine, using a SQL-like language (DQL):


```dataview
TABLE status, date
FROM #scrum
WHERE date >= date(today) - dur(7 days)
SORT date DESC
```

Dataview still has a place, but for the majority of use cases it's too complex and will be replaced by Bases. Where it _still wins_: aggregating tasks — collecting every checkbox across a set of notes into one dynamic to-do list — and embedding queries inside templates, which Bases can't do. [Effortless Academic](https://effortlessacademic.com/using-obsidian-bases-for-academic-note-taking/)[Effortless Academic](https://effortlessacademic.com/using-obsidian-bases-for-academic-note-taking/)

Practical call: use **Bases for tables/overviews, Dataview only when you hit something Bases can't do.** Note there's also **Datacore**, Dataview's WIP successor — faster and more native-feeling, but it scraps the friendly query syntax and forces you to build components in React. Skip it unless you want to build reusable component libraries. [Obsidian Rocks](https://obsidian.rocks/dataview-vs-datacore-vs-obsidian-bases/)

# Templater — Templates with a Brain

This is the upgrade to core Templates I flagged in Stage 3. Where core Templates only does `{{date}}`, Templater adds:

- JavaScript execution inside templates
- Cursor placement after insertion (`<% tp.file.cursor() %>`)
- Prompts that ask you for input when inserting
- System commands, date math, file operations

This is what powers the "carryover unchecked tasks from yesterday" daily-note feature I mentioned earlier. If you do any template automation, you'll want this.

# Tasks — Task Management Across the Vault

Lets you write tasks with due dates, priorities, and recurrence anywhere, then query them globally:

markdown

```markdown
- [ ] Fix luz-thumbnail HPA ratio 📅 2026-05-28 ⏫ #klara
```

A Tasks query block then aggregates every open task across all notes into one filtered view. It's the cleaner alternative to doing task aggregation in Dataview, and it pairs naturally with your daily scrum template — every `- [ ]` you write in a standup becomes trackable in one place.

# Calendar — Visual Daily-Note Navigation

A small month-calendar in the sidebar. Click a day to open (or create) that daily note; dots indicate which days have notes. Trivial but high-value if Daily Notes is central to your workflow — it turns your timeline into something you navigate visually instead of by filename.

# Excalidraw — Hand-Drawn Diagrams

A full sketching canvas inside Obsidian, storing drawings as notes you can link and embed. Good for architecture sketches — the kind of Luz service-topology or Pub/Sub-flow diagrams you'd otherwise do in Mermaid. It complements rather than replaces Mermaid (which Obsidian renders natively in code fences): Mermaid for structured/version-controllable diagrams, Excalidraw for freeform whiteboarding.

# Recommended Stage 4 Install Order

For your profile, install in this sequence and let each settle before adding the next:

1. **Bases** (enable the core feature) — querying without code
2. **Tasks** — aggregate the checkboxes from your scrum template
3. **Templater** — automate the daily/meeting templates
4. **Calendar** — navigate the daily-note timeline
5. **Excalidraw** — when you need a sketch Mermaid can't do easily

Resist installing 30 plugins. Each one is maintenance surface and a potential slowdown. The five above cover query, task, automation, navigation, and diagramming — which is a complete system.

---

**End-of-stage checkmark:** you have a Base that rolls up your scrum notes into a live table, your standup checkboxes surface in a single Tasks view, and at least one template does something dynamic via Templater. At that point you're running a genuine knowledge system, and Stage 5 (methodology — Zettelkasten / PARA) is about deciding _how to think_ inside it rather than which buttons to press.