# Social Media Share Buttons for Webflow

A lightweight JavaScript snippet that adds **social media share buttons** to your Webflow site, with no plugins or extra libraries. It supports:

- Facebook
- WhatsApp
- Threads
- LinkedIn
- Twitter / X
- Copy Link, with a toast notification

---

## Table of Contents

1. [How It Works](#how-it-works)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Building the Buttons in Webflow Designer](#building-the-buttons-in-webflow-designer)
5. [Attribute Reference](#attribute-reference)
6. [Function Reference](#function-reference)
7. [Customization](#customization)
8. [Testing](#testing)
9. [Troubleshooting](#troubleshooting)
10. [Important Notes](#important-notes)

---

## How It Works

1. When the page finishes loading (`DOMContentLoaded`), the script finds every element that has a `data-social` attribute.
2. It reads the page data:
   - **Link** from `window.location.href` (the current page URL)
   - **Title** from the `og:title` meta tag
   - **Description** from the `og:description` meta tag
   - **Image** from the `og:image` meta tag
3. When a button is clicked, the script opens a new window (or navigates the page, for LinkedIn on mobile) to the matching platform's share URL.
4. The element with `id="copyLink"` copies the page URL to the clipboard and shows a toast for 2 seconds.

---

## Prerequisites

- A Webflow **Site Plan** or **Workspace** plan that supports **Custom Code**.
- **Open Graph** meta tags filled in on your pages. In Webflow, go to **Page Settings → Open Graph Settings** and set the Title, Description, and Image. For CMS pages, bind them to CMS fields.

---

## Installation

There are two places you can add the script.

### Option A: Site-wide (recommended)

1. Open **Site Settings → Custom Code**.
2. Paste the script into the **Footer Code** section (before the `</body>` tag).
3. Click **Save Changes**, then **Publish**.

### Option B: Specific page only

1. Open **Pages** and click the gear icon (⚙) next to the page you want.
2. Scroll to **Custom Code → Before `</body>` tag**.
3. Paste the script, save, then **Publish**.



## Building the Buttons in Webflow Designer

### 1. Platform Share Buttons

1. Add a **Link Block** (or **Button** / **Div Block**) for each platform.
2. Put an icon or text inside it.
3. Open **Element Settings (D) → Custom Attributes** and add:

| Platform  | Name          | Value       |
|-----------|---------------|-------------|
| Facebook  | `data-social` | `facebook`  |
| WhatsApp  | `data-social` | `whatsapp`  |
| Threads   | `data-social` | `threads`   |
| LinkedIn  | `data-social` | `linkedin`  |
| Twitter/X | `data-social` | `twitter`   |

> The script calls `event.preventDefault()`, so a Link Block can be left without a destination URL.

### 2. Copy Link Button

1. Add a **Link Block** or **Button**.
2. Set the element **ID** to `copyLink` (Element Settings → ID).

### 3. Toast Notification

1. Add a **Div Block** containing a message such as "Link copied!".
2. Set the element **ID** to `toast`.
3. Set **Display** to **None** so it is hidden by default.
4. Style it as a notification, for example **Position: Fixed**, near the bottom center of the screen, with a high **z-index**.

> If the `#toast` element is missing, the link will still be copied, but an error will appear in the console because the toast cannot be found.

### Example HTML (Final Result)

```html
<div class="share-wrapper">
  <a href="#" data-social="facebook">Facebook</a>
  <a href="#" data-social="whatsapp">WhatsApp</a>
  <a href="#" data-social="threads">Threads</a>
  <a href="#" data-social="linkedin">LinkedIn</a>
  <a href="#" data-social="twitter">Twitter / X</a>
  <a href="#" id="copyLink">Copy Link</a>
</div>

<div id="toast" style="display:none;">Link copied!</div>
```

### Example Toast CSS (optional)

Add it through an **Embed** element or **Page Settings → Inside `<head>` tag**:

```html
<style>
  #toast {
    position: fixed;
    bottom: 24px;
    left: 50%;
    transform: translateX(-50%);
    background: #111;
    color: #fff;
    padding: 10px 20px;
    border-radius: 8px;
    font-size: 14px;
    z-index: 9999;
  }
</style>
```

---

## Attribute Reference

| Attribute / ID       | Purpose                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------|
| `data-social="..."`  | Sets the target platform. Valid values: `facebook`, `whatsapp`, `threads`, `linkedin`, `twitter`  |
| `id="copyLink"`      | Marks the button that copies the page URL                                                         |
| `id="toast"`         | The notification element shown for 2 seconds after the link is copied                             |

---

## Function Reference

| Function                                            | Description                                                                         |
|-----------------------------------------------------|-------------------------------------------------------------------------------------|
| `isMobile()`                                        | Detects Android/iOS devices via `navigator.userAgent`                               |
| `shareToFacebook(link, title, description, image)`  | Opens the Facebook Sharer                                                           |
| `shareToWhatsApp(link, title)`                      | Opens WhatsApp with the text `title + link`                                         |
| `shareToThreads(link, title, image)`                | Opens the Threads post intent                                                       |
| `shareToLinkedIn(link, title, description)`         | Desktop: opens a new tab. Mobile: redirects the page to `shareArticle`              |
| `shareToTwitter(link, title)`                       | Opens the Twitter/X tweet intent                                                    |
| `copyURL()`                                         | Copies the URL to the clipboard and shows the toast                                 |

---

## Customization

**Change the toast duration**: edit the `2000` (milliseconds) value in the `setTimeout` call inside `copyURL()`.

**Add a new platform** (Telegram as an example):

```js
function shareToTelegram(link, title) {
    window.open('https://t.me/share/url?url=' + encodeURIComponent(link) +
        '&text=' + encodeURIComponent(title), '_blank');
}
```

Then add it to the `if / else if` block:

```js
else if (platform === 'telegram') shareToTelegram(link, title);
```

Finally, give the button the attribute `data-social="telegram"`.

**Use a canonical URL**: to always share a specific URL instead of the one currently open, replace:

```js
const link = window.location.href;
```

with:

```js
const link = document.querySelector('link[rel="canonical"]')?.href || window.location.href;
```

---

## Testing

1. **Publish** your site (custom code does not fully run in Designer mode).
2. Open the published page in a browser.
3. Click each button and confirm the share window opens with the correct URL.
4. Click **Copy Link**, paste it somewhere, and confirm the URL was copied and the toast appears.
5. Test on a mobile device too, especially LinkedIn and WhatsApp.
6. Validate your Open Graph previews with tools like the Facebook Sharing Debugger or LinkedIn Post Inspector (you may need to refresh their cache).

---

## Troubleshooting

| Problem                                   | Likely Cause and Fix                                                                                                   |
|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Buttons do nothing                        | `data-social` is misspelled, the site is not published, or the script was placed before the elements (use Footer Code) |
| Popup blocked by the browser              | Make sure `window.open` is triggered directly by a user click (it is, in this script)                                  |
| Copy link fails                           | `navigator.clipboard` only works over **HTTPS** (or `localhost`). Make sure SSL is enabled on your published site      |
| `toast is null` error in the console      | The element with `id="toast"` has not been created                                                                     |
| Preview (title/image) is wrong            | Open Graph tags are missing or the platform cache is stale. Fill in the OG settings and refresh via the debugger tool  |
| Empty title on WhatsApp/Twitter           | The `og:title` meta tag is not set in Page Settings                                                                    |
| Works in Designer but not live            | Custom code only runs on a published site                                                                              |

---

## Important Notes

- **Facebook**: the modern Facebook Sharer mainly takes the title, description, and image from the page's Open Graph tags, not from the `title`, `description`, or `picture` URL parameters. The script sends those parameters, but they are most likely ignored. Make sure your OG tags are set correctly.
- **Threads**: the `image_url` parameter may not be supported by the Threads post intent. Previews usually follow the OG tags of the shared URL.
- **Twitter/X**: the script sends both `url` and a `text` that also contains the link, so the link may appear twice in the tweet draft. To avoid this, send only the title as `text`:

  ```js
  function shareToTwitter(link, title) {
      let shareUrl = 'https://twitter.com/intent/tweet?url=' + encodeURIComponent(link) +
          '&text=' + encodeURIComponent(title);
      window.open(shareUrl, '_blank');
  }
  ```

- **Data is read on page load**: if meta tag content changes dynamically after load, the `title`, `description`, and `image` values will not update.
- **No tracking**: the script sends no data to third-party servers; it only opens each platform's official share URL.

---

## License

Free to use and modify for your own projects.
