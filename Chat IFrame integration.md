## Lena Chat iframe Integration Guide

Lena Chat can be embedded inside any website using a standard HTML `<iframe>`.
Each client gets a dedicated `client_id`; this ID controls **which account** and
**which theme/branding** the user sees.

---

### 1. Endpoint & client ID

- **Chat URL base**: `https://chat.lenaai.net`
- **Required identifier**: `client` (we provide this value to you, e.g. `DREAMHOMES_EG`).

You can provide `client` in two equivalent ways:

1. **Recommended for iframes – query parameter**

   ```text
   https://chat.lenaai.net?client=YOUR_CLIENT_ID
   ```

2. **Alternative – path segment (mainly for direct navigation)**

   ```text
   https://chat.lenaai.net/YOUR_CLIENT_ID
   ```

For iframe usage, prefer the **query parameter form**. The path form is kept for
backwards compatibility / direct browser usage.

---

### 2. Basic iframe embed

Place this HTML where you want the chat widget to appear on your page and replace
`YOUR_CLIENT_ID`:

```html
<iframe
  src="https://chat.lenaai.net?client=YOUR_CLIENT_ID"
  style="width: 100%; max-width: 420px; height: 640px; border: none; border-radius: 16px; box-shadow: 0 12px 40px rgba(0,0,0,0.18);"
  loading="lazy"
  allow="microphone; clipboard-read; clipboard-write"
></iframe>
```

This will render the full Lena chat experience inside your website.

---

### 3. Example for a specific client

If your assigned `client` is `DREAMHOMES_EG`:

```html
<iframe
  src="https://chat.lenaai.net?client=DREAMHOMES_EG"
  style="width: 100%; max-width: 420px; height: 640px; border: none; border-radius: 16px; box-shadow: 0 12px 40px rgba(0,0,0,0.18);"
  loading="lazy"
  allow="microphone; clipboard-read; clipboard-write"
></iframe>
```

---

### 4. Supported URL parameters (current implementation)

The current implementation supports the following URL parameters:

- **`client` (required)**  
  - Identifies your account and loads your configuration and theme.  
  - Controls which data and backend configuration are used.

- **`campaign` (optional)**  
  - Tags the chat with a specific marketing campaign.  
  - Example: `campaign=XWppO7T1`.  
  - If omitted, the system falls back to using `client` as the campaign identifier.

- **`source` (optional)**  
  - Free‑text tag to help you track where the chat was opened from.  
  - Examples: `homepage`, `listing_page`, `campaign_summer_2025`, etc.

- **`force_user_info_request` (optional, advanced)**  
  - Allows you to override whether the chat should force asking for **name and phone**.  
  - Values:
    - `force_user_info_request=true` or `1` → always show the user info form when missing.  
    - `force_user_info_request=false` or `0` → do **not** auto-open the user info form in iframe mode.  
  - The user info form is shown when:
    - A `campaign` is present **or** `force_user_info_request` is true, **and**
    - The user’s name/phone are not stored yet.

Example with `source`:

```html
<iframe
  src="https://chat.lenaai.net?client=YOUR_CLIENT_ID&campaign=XWppO7T1&source=homepage&force_user_info_request=false"
  style="width: 100%; max-width: 420px; height: 640px; border: none; border-radius: 16px; box-shadow: 0 12px 40px rgba(0,0,0,0.18);"
  loading="lazy"
  allow="microphone; clipboard-read; clipboard-write"
></iframe>
```

> At the moment, these are the only URL attributes used.  
> All theme/branding changes are controlled **on the Lena side** based on your
> `client` (no extra theme query parameters are needed).

---

### 5. Theme & branding

You do **not** need to pass any additional URL parameters for theme or styling.

- The app reads your **`client_id`**.
- Based on that `client_id`, Lena Chat loads your:
  - Display name (e.g. “DreamHomes.eg”),
  - Colors and other branding, as configured in our system.

If you want to change:

- Chat header name,
- Main color,
- Logo or other theme aspects,

contact Lena support and we will update the configuration for your `client` (client ID).
You keep the same iframe `src` URL; no code change on your side is required.

---

### 6. Layout & positioning examples

You can control position and size entirely via CSS on your site.

#### 6.1. Centered block widget

```html
<div style="max-width: 420px; margin: 0 auto;">
  <iframe
    src="https://chat.lenaai.net?client=YOUR_CLIENT_ID"
    style="width: 100%; height: 640px; border: none; border-radius: 16px; box-shadow: 0 12px 40px rgba(0,0,0,0.18);"
    loading="lazy"
    allow="microphone; clipboard-read; clipboard-write"
  ></iframe>
</div>
```

#### 6.2. Floating bottom‑right widget

```html
<style>
  #lena-chat-widget {
    position: fixed;
    bottom: 20px;
    right: 20px;
    width: 360px;
    max-width: 100%;
    height: 520px;
    border: none;
    border-radius: 16px;
    box-shadow: 0 12px 40px rgba(0, 0, 0, 0.18);
    z-index: 9999;
  }
</style>

<iframe
  id="lena-chat-widget"
  src="https://chat.lenaai.net?client=YOUR_CLIENT_ID"
  loading="lazy"
  allow="microphone; clipboard-read; clipboard-write"
></iframe>
```

You can show/hide this iframe with your own JavaScript (for example, when a user
clicks a “Chat with us” button).

---

### 7. Permissions

The iframe needs some browser permissions to enable certain features:

- `microphone` – for voice input.
- `clipboard-read` / `clipboard-write` – for copy/paste convenience.

Make sure the iframe includes:

```html
allow="microphone; clipboard-read; clipboard-write"
```

---

### 8. Path‑based usage (optional)

For direct testing in a browser (not embedded), you can also open:

```text
https://chat.lenaai.net/YOUR_CLIENT_ID
```

This behaves like the iframe version but uses a path instead of
`?client=...`. Both forms are supported; for production embeds we recommend
the **query parameter form** for clarity.

---

### 9. Checklist before going live

- [ ] The `client` in the iframe `src` is correct (provided by Lena).  
- [ ] The iframe is visible and not clipped/overlapped by other elements.  
- [ ] The `allow="microphone; clipboard-read; clipboard-write"` attribute is present.  
- [ ] Tested on desktop and mobile (layout and responsiveness look good).

---

### 10. Support / changes

If you need:

- A new `client` value for another brand or website,
- Changes to theme/branding linked to your `client`,
- Help integrating in specific environments (WordPress, React, Vue, etc.),

please contact the Lena team and share:

- Your website URL,
- Your `client`,
- Where you want the chat to appear,
- Any special requirements you have.


