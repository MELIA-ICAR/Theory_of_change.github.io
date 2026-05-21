# Setting up the MELIA submission registry

> A step-by-step guide for the MELIA team to enable user-submitted Theories of Change in the Theory of Change Builder.

This guide walks you through linking the chatbot to a private Google Form, so that when users finish their ToC and click **Submit to MELIA**, the data lands in a Google Sheet you control.

**Time required:** ~15 minutes. **Cost:** Free. **Account needed:** A Google account (institutional or personal — your choice).

---

## What you're building

```
┌──────────────┐       ┌─────────────────┐       ┌──────────────────┐
│  Chatbot     │──────►│  Google Form    │──────►│  Google Sheet    │
│  (browser)   │ POST  │  (invisible)    │       │  (your registry) │
└──────────────┘       └─────────────────┘       └──────────────────┘
```

The Google Form acts purely as a receiving endpoint. Users never see it — they interact only with the chatbot. The Form quietly stores each submission in a connected Google Sheet that only you and people you authorise can view.

---

## Step 1 — Create the Google Form

1. Go to **https://forms.google.com** and sign in with the Google account that should own the registry. *(Tip: use an institutional account if possible — for example a `melia.icar@gmail.com` shared account — so the registry survives staff changes.)*

2. Click **+ Blank form**.

3. Set the **title** to: `MELIA ToC Registry`

4. Set the **description** to: `Submissions from the Theory of Change Builder. This form is not for public use.`

5. Add the following questions, **in this exact order**. For each, click **+ Add question**, set the question type to **Short answer** (or **Paragraph** for the longer fields), and use the exact text below as the question:

| # | Question text          | Type           |
|---|------------------------|----------------|
| 1 | User name              | Short answer   |
| 2 | Institute              | Short answer   |
| 3 | Email                  | Short answer   |
| 4 | Project name           | Short answer   |
| 5 | Long-term Impact       | Paragraph      |
| 6 | Outcomes               | Paragraph      |
| 7 | Outputs                | Paragraph      |
| 8 | Activities and Inputs  | Paragraph      |
| 9 | Assumptions            | Paragraph      |
| 10| Indicators             | Paragraph      |
| 11| Validation             | Paragraph      |
| 12| Notes                  | Paragraph      |
| 13| Submitted at           | Short answer   |

Do **not** mark any as required — the chatbot already validates, and marking them required will cause silent submission failures if any field is empty.

6. Click the **⚙ Settings** icon (top right). Under the **Responses** tab:
   - Turn **OFF** "Collect email addresses" (the chatbot collects this itself).
   - Turn **OFF** "Limit to 1 response" (researchers will use the tool many times).
   - Turn **OFF** "Allow response editing" (each submission should be immutable).

7. Save.

---

## Step 2 — Get the form action URL

This is the URL the chatbot will POST to.

1. With your form open, click **Send** (top right).
2. Click the **link icon** (🔗). You'll see a URL like `https://docs.google.com/forms/d/e/1FAIpQLSe.../viewform`.
3. **Copy that URL.**
4. Replace the word **`viewform`** at the end with **`formResponse`**.

You now have a URL like:

```
https://docs.google.com/forms/d/e/1FAIpQLSe_LONG_RANDOM_STRING_abc/formResponse
```

**Save this URL** — you'll paste it into `index.html` in Step 4.

---

## Step 3 — Get each field's entry ID

Each question has a hidden "entry ID" that the chatbot needs to reference.

1. On your form's edit page, click the **⋮** (three dots, top right) and choose **Get pre-filled link**.
2. Type any placeholder text into each field — anything, like "test1" in the first field, "test2" in the second, and so on. This unlocks the link.
3. Click **Get link** at the bottom, then **Copy link**.
4. Paste the link into a text editor. You'll see something like:

   ```
   https://docs.google.com/forms/d/e/1FAIpQLSe.../viewform?usp=pp_url&entry.1234567890=test1&entry.0987654321=test2&entry.1122334455=test3...
   ```

5. Each `entry.XXXXXXXXXX` is the ID for one question, in the order they appear on your form.

**Map them like this** (write each one down next to its question):

| # | Question              | Field key in code | entry ID         |
|---|-----------------------|-------------------|------------------|
| 1 | User name             | `user`            | entry.XXXXXXXXX  |
| 2 | Institute             | `institute`       | entry.XXXXXXXXX  |
| 3 | Email                 | `email`           | entry.XXXXXXXXX  |
| 4 | Project name          | `project`         | entry.XXXXXXXXX  |
| 5 | Long-term Impact      | `impact`          | entry.XXXXXXXXX  |
| 6 | Outcomes              | `outcomes`        | entry.XXXXXXXXX  |
| 7 | Outputs               | `outputs`         | entry.XXXXXXXXX  |
| 8 | Activities and Inputs | `activities`      | entry.XXXXXXXXX  |
| 9 | Assumptions           | `assumptions`     | entry.XXXXXXXXX  |
| 10| Indicators            | `indicators`      | entry.XXXXXXXXX  |
| 11| Validation            | `validation`      | entry.XXXXXXXXX  |
| 12| Notes                 | `notes`           | entry.XXXXXXXXX  |
| 13| Submitted at          | `timestamp`       | entry.XXXXXXXXX  |

---

## Step 4 — Paste into `index.html`

1. Open `index.html` in any text editor.
2. Find the block near the top of the `<script>` section that begins:

   ```javascript
   const GOOGLE_FORM = {
     formActionUrl: "PASTE_YOUR_FORM_RESPONSE_URL_HERE",
     fields: {
       user:        "entry.111111111",
       institute:   "entry.222222222",
       ...
     }
   };
   ```

3. Replace `"PASTE_YOUR_FORM_RESPONSE_URL_HERE"` with your URL from Step 2.
4. Replace each placeholder `entry.111111111`, `entry.222222222`, etc. with the real entry IDs from Step 3 — matching field-by-field.

Example of a completed block:

```javascript
const GOOGLE_FORM = {
  formActionUrl: "https://docs.google.com/forms/d/e/1FAIpQLSeABC.../formResponse",
  fields: {
    user:        "entry.1234567890",
    institute:   "entry.0987654321",
    email:       "entry.1122334455",
    project:     "entry.5566778899",
    impact:      "entry.4433221100",
    outcomes:    "entry.6677889900",
    outputs:     "entry.9988776655",
    activities:  "entry.2233445566",
    assumptions: "entry.7788990011",
    indicators:  "entry.3344556677",
    validation:  "entry.8899001122",
    notes:       "entry.4455667788",
    timestamp:   "entry.9900112233"
  }
};
```

5. Save the file and push to your GitHub repository. GitHub Pages will redeploy automatically within a minute or two.

---

## Step 5 — Connect a Google Sheet

This is where the submitted data will accumulate.

1. Back in your Google Form, click the **Responses** tab.
2. Click the **Link to Sheets** icon (green spreadsheet icon).
3. Choose **Create a new spreadsheet** → name it `MELIA ToC Registry — Responses`.
4. Click **Create**.

That's it. Every future submission appears as a new row in this sheet, with one column per question plus an automatic timestamp column.

You can:
- Sort and filter by institute, date, or project name.
- Share read access with other MELIA team members via the Sheet's **Share** button.
- Set up email notifications under the Responses tab (⋮ → **Get email notifications for new responses**).

---

## Step 6 — Test it

1. Open your live chatbot page (e.g. `https://melia-icar.github.io/ToC/`).
2. Enter test details on the welcome screen (use "TEST" as your name to mark it).
3. Walk through all 8 steps with short test answers.
4. Click **Submit to MELIA**, check the consent box, and click **Submit Now**.
5. You should see the green "Thank you" confirmation.
6. Open your Google Sheet — your test row should appear within a few seconds.

**If nothing arrives:**
- Open your browser's developer console (F12 → Console). Check for red errors.
- Most common cause: a mismatched entry ID. Re-do Step 3 carefully.
- Second most common: you used the `viewform` URL instead of `formResponse` in Step 2.

When the test works, **delete the test row** from your Google Sheet to keep the registry clean.

---

## Step 7 — Optional: privacy & retention

A few good-practice settings you may want to put in place:

- **Restrict Sheet access** to named MELIA team members only. Don't make it public.
- **Set a retention policy** — e.g., delete submissions after 3 years unless the user requests longer storage.
- **Create a data-request inbox** (e.g. `melia.icar+toc@gmail.com`) so users can request access, correction, or deletion of their submission, as promised in the consent text.
- **Add a contact email** to the chatbot's README so users know who to reach.
- **Periodically audit** the Sheet for accidental sensitive data (the tool reminds users not to share confidential info, but mistakes happen).

---

## Disabling submissions temporarily

If you ever want to turn off submissions (during maintenance, or while updating the form):

1. Open `index.html`.
2. Change the line:
   ```javascript
   formActionUrl: "https://docs.google.com/forms/...",
   ```
   back to:
   ```javascript
   formActionUrl: "PASTE_YOUR_FORM_RESPONSE_URL_HERE",
   ```
3. Push to GitHub. The **Submit to MELIA** button will quietly disappear from the chatbot until you re-enable it. Users can still download PPTX and JSON.

---

## Troubleshooting

**"I added a question to my form and now nothing works."**
Adding questions can reorder entry IDs. Redo Step 3 to capture the new IDs, then update `index.html`.

**"Submissions arrive but some fields are empty."**
Most likely cause: an entry ID is wrong, so that question's data is being sent to the wrong field — or to a non-existent one. Test with a known string in each field and trace which column it lands in.

**"The button says 'Submission failed' but the data still arrives in the Sheet."**
This is normal in some browsers. Google Forms uses a "no-cors" response that the browser cannot read, so the chatbot occasionally cannot confirm success. The submission still went through. If this is frequent, consider switching to a real form backend (Formspree, Basin) — see the README.

**"I want to switch to a different backend later."**
The `submitToMelia()` function in `index.html` is the only place that talks to Google. Replace its body with a call to any other endpoint (Formspree, your own server, a Cloudflare Worker) and everything else continues to work.

---

## Summary

You now have:

✅ A private Google Form receiving submissions.
✅ A Google Sheet where every submission appears as a row.
✅ A working **Submit to MELIA** button in the chatbot.
✅ Email notifications (if you turned them on).
✅ A documented data-request process for users.

For questions, open an issue on the project repository at `https://github.com/MELIA-ICAR/ToC`.
