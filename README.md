# Theory of Change Builder

> A conversational, browser-based tool for developing a **Theory of Change (ToC)** framework for agricultural research projects and programmes — aligned with ICAR goals and national priorities.

A single-page chatbot that walks researchers, project managers, and extension staff through the 8 canonical steps of building a ToC, then assembles their answers into a downloadable PowerPoint with an impact pathway diagram. Optionally, users may consent to submit their final ToC to a private MELIA registry.

No backend. No build step. No login. Drop one HTML file on GitHub Pages and it works.

---

## ✦ Features

- **Personalised welcome** — captures user name, institute, email (optional), and project name
- **Guided 8-step conversation** — bot asks one question per step with a tailored hint and worked example
- **Editable at any time** — jump to any step from the sidebar, edit the last answer, or skip ahead
- **Visual impact pathway** — auto-generated SVG diagram showing Inputs → Outputs → Outcomes → Impact
- **Personalised PowerPoint export** — title slide shows preparer's name and institute
- **JSON export** — save the full framework as structured data
- **Voluntary submission to MELIA** — with explicit consent, send your final ToC to the MELIA registry (Google Sheets backend)
- **Privacy-first design** — nothing leaves the browser unless the user clicks Submit and consents
- **Pure static** — runs entirely in the browser, deployable to GitHub Pages

---

## ✦ The 8 Steps

| # | Step | What it captures |
|---|------|------------------|
| 1 | **Long-term Impact** | The 5–10+ year change envisaged, aligned with ICAR and national priorities |
| 2 | **Outcomes** | Medium-term changes in behaviour, practice, or systems |
| 3 | **Outputs** | Direct products and deliverables from project activities |
| 4 | **Activities / Inputs** | Interventions, actions, and resources that produce outputs |
| 5 | **Assumptions** | Conditions that must hold true; external factors that could derail the pathway |
| 6 | **Indicators** | SMART measures for each level (activity → impact) |
| 7 | **Validation** | Stakeholder engagement that confirms the causal pathways are realistic |
| 8 | **Visualisation & Notes** | Final notes; triggers diagram generation and PPTX export |

---

## ✦ Quick start

### Option 1 — GitHub Pages (recommended)

1. Fork or download this repo
2. Push it to your own GitHub account
3. Go to **Settings → Pages**
4. Under **Source**, select `main` branch and `/ (root)` folder
5. Save. Your tool is live at `https://<your-username>.github.io/<repo-name>/`

### Option 2 — Run locally

Just open `index.html` in any modern browser.

```bash
# or, if you prefer a local server
python3 -m http.server 8000
# then visit http://localhost:8000
```

### Option 3 — Other static hosts

Works on Netlify, Vercel, Cloudflare Pages, Surge, or any static host.

---

## ✦ Enabling MELIA submissions (optional but recommended)

The chatbot ships with a **Submit to MELIA** button. By default it is disabled — when configured, it lets users voluntarily send their completed ToC to a private Google Sheet that the MELIA team controls.

**To enable submissions**, follow the step-by-step guide in [`SETUP_GOOGLE_FORM.md`](SETUP_GOOGLE_FORM.md). It takes about 15 minutes and requires only a Google account.

When the configuration block in `index.html` still contains placeholders (`PASTE_YOUR_FORM_RESPONSE_URL_HERE`), the submit button is hidden automatically. Users can still download PPTX and JSON.

---

## ✦ Privacy & data handling

This tool was designed with **transparent, consent-based** data practices, in alignment with:

- India's **Digital Personal Data Protection Act, 2023** (DPDP Act)
- **ICAR research ethics norms** for handling researcher and institutional data
- Standard **MELIA confidentiality** expectations

### What stays in the browser by default

- All 8 ToC answers
- User name, institute, email, and project name (collected on the welcome screen)

Closing the browser tab clears this data. Nothing is uploaded automatically.

### What is shared with the MELIA team (only on consent)

Only when a user clicks **Submit to MELIA** *and* ticks the consent checkbox:

- Name, institute, email (if provided), project name
- All 8 ToC answers
- Timestamp of submission

This goes to a private Google Sheet visible only to authorised MELIA team members.

### What is never collected

- Browser fingerprints, IP-address logging, analytics, or tracking cookies
- Anything beyond what the user explicitly types in
- Anything before the user clicks Submit

### User rights

Users can request access, correction, or deletion of their submission by contacting the MELIA team. See `SETUP_GOOGLE_FORM.md` Step 7 for setting up a contact channel.

---

## ✦ How it works

```
┌─────────────────────────────────────────────┐
│  index.html  (single file, ~45 KB)          │
│  ├── HTML structure + welcome modal          │
│  ├── CSS (paper-grain editorial aesthetic)   │
│  └── Vanilla JavaScript                      │
│      ├── Step state machine                  │
│      ├── Chat renderer                       │
│      ├── SVG pathway diagram generator       │
│      ├── PPTX export (via PptxGenJS CDN)     │
│      └── Submit-to-MELIA (Google Forms POST) │
└─────────────────────────────────────────────┘
```

**Dependencies** — exactly one, loaded from CDN:

- [PptxGenJS](https://github.com/gitbrent/PptxGenJS) for the PowerPoint export

Fonts (Fraunces, JetBrains Mono, Inter Tight) come from Google Fonts.

---

## ✦ File structure

```
toc-builder/
├── index.html               ← the main app
├── README.md                ← this file
├── SETUP_GOOGLE_FORM.md     ← how to enable MELIA submissions
└── ToC_Builder_User_Manual.docx  ← end-user manual (optional)
```

---

## ✦ Customising

### Change the visual identity

All colours live as CSS variables at the top of the `<style>` block in `index.html`:

```css
:root {
  --paper: #f4efe6;       /* background */
  --ink: #1a1a1a;         /* primary text */
  --accent: #c8542b;      /* terracotta */
  --accent-2: #4a6b3a;    /* leaf green */
  --accent-3: #d4a017;    /* harvest gold */
}
```

### Change the questions

Edit the `STEPS` array in `index.html`. Each step has a `prompt`, `hint`, and `example`.

### Change the welcome modal fields

Edit the HTML inside `<div id="welcome-modal">` and update the `state.user` object and `submitToMelia()` function to match.

### Translate

The conversation text is all in `index.html`, in the `STEPS` array and inline HTML. Find-and-replace can take the tool into any regional language.

---

## ✦ Roadmap & ideas for extension

- **Multi-project support** — save several ToCs side-by-side
- **Branching pathways** — multiple outcomes per output, rendered as branching arrows
- **Risk register** — for each assumption, ask likelihood and impact, auto-generate a risk matrix
- **Stakeholder review mode** — export a read-only review page with embedded comment boxes
- **SDG / ICAR pillar tagging** — checkbox alignment scoring
- **Word / PDF export** — narrative-format deliverables
- **AI-assisted critique** — optional "Review my ToC" button via the Anthropic API
- **Submission analytics dashboard** — visualise patterns across institutes (aggregated, anonymised)

Pull requests welcome.

---

## ✦ Background

A **Theory of Change** is a structured articulation of *how* and *why* a programme is expected to produce a desired change. Unlike a logframe, which is largely descriptive, a ToC makes the **causal logic and underlying assumptions explicit**, allowing them to be tested, contested, and refined.

The **impact pathway** is the operational layer of a ToC — it traces the sequence:

```
Research inputs → Outputs (technology, knowledge) → Outcomes (adoption, behaviour) → Impact
```

…with assumptions and external factors annotated at each transition, and SMART indicators at each level.

This tool turns what is often a multi-day workshop exercise into something a researcher can draft in a single sitting, then take into a stakeholder workshop for validation.

---

## ✦ License

MIT — use it, fork it, adapt it for your institution.

---

## ✦ Acknowledgements

Built for ICAR MELIA. If you use this for a real project, please share what you learned. Issues and pull requests are welcome.

For data-related queries about the MELIA registry, contact the MELIA team.
