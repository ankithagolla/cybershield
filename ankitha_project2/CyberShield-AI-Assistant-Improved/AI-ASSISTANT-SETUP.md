# Connect the CyberShield AI assistant

The assistant now calls the Gemini API through a Netlify Function. The Gemini secret key stays on Netlify's server. The function also checks the signed-in user's Supabase access token before it calls Gemini. The website does not include a provider key.

## Before you start

- A Gemini API key from Google AI Studio. Free-tier model access is subject to provider quotas and terms.
- Your Supabase Project URL and publishable key.
- The Netlify site `cybershield-ai-safety`.
- Node.js installed if you use the Netlify CLI route below.

## 1. Restore the Supabase browser settings

Open `site/auth-config.js`. If the `url` and `anonKey` fields are blank, fill them with the Supabase Project URL and publishable key. Keep the property names and quotation marks. The publishable key is safe for browser use; never put a secret or `service_role` key here.

```js
window.CYBERSHIELD_SUPABASE_CONFIG = {
  url: "https://YOUR-PROJECT-REF.supabase.co",
  anonKey: "sb_publishable_YOUR_PUBLIC_KEY"
};
```

Keep your existing Supabase Auth Site URL and Redirect URL set to `https://cybershield-ai-safety.netlify.app`.

## 2. Add server-only variables in Netlify

In Netlify, open the `cybershield-ai-safety` project and go to **Project configuration → Environment variables**. Add these variables for the **Production** deploy context and make them available to **Functions** (or all scopes if the UI does not offer a Functions scope):

| Name | Value |
| --- | --- |
| `GEMINI_API_KEY` | A Gemini API key from Google AI Studio (mark it as a secret) |
| `SUPABASE_URL` | The Supabase Project URL, such as `https://YOUR-PROJECT-REF.supabase.co` |
| `SUPABASE_PUBLISHABLE_KEY` | The Supabase publishable key (`sb_publishable_...`); a legacy anon key also works |

Do not put `GEMINI_API_KEY` in `app.js`, `site/auth-config.js`, GitHub, or a public `.env` file. Keep the project on the Gemini free tier and do not link paid billing if you need to avoid charges. Free-tier quotas can be reached, and free-tier prompts may be used to improve Google's products; do not send passwords, bank details, or other sensitive information.

## 3. Deploy the function (important)

**Do not use Netlify Drop / drag-and-drop for this update.** A static drag-and-drop deploy does not package the `netlify/functions` server function. Deploy this folder using one of these methods:

### Netlify CLI

Open PowerShell in the extracted project folder (the one containing `netlify.toml`; do not `cd` into `site`):

```powershell
npm install -g netlify-cli
netlify login
netlify link
netlify deploy --prod
```

When prompted by `netlify link`, select the existing `cybershield-ai-safety` site. `netlify.toml` configures `site` as the website folder and `netlify/functions` as the function folder. Review the deploy summary and confirm it targets the existing site before publishing.

### Git-connected deploy

Commit the full extracted project folder, including `site/`, `netlify.toml`, and `netlify/functions/cybershield-chat.mjs`, to the GitHub repository connected to Netlify. In Netlify's project configuration, connect the existing site to that repository if it is not connected already. `netlify.toml` points Netlify to the site and function folders. Push the changes and wait for the production deploy to finish.

## 4. Check the live assistant

Sign in at `https://cybershield-ai-safety.netlify.app`, open **Ask CyberShield**, and send a simple question such as “What is phishing?”. The assistant should answer from the model and remember recent turns while this page is open. Refreshing the page clears that chat history.

- A `503` message means a required Netlify environment variable is missing or a provider is unavailable; a `404` usually means the function was not deployed.
- A `401` message means the Supabase session expired; sign in again.
- A `429` message means the free provider quota was reached or the service is busy; retry later or review the provider's quota page.

## Data and safety

Questions and up to 12 recent chat turns are sent to the Netlify Function and then to OpenAI. `store: false` is set for the Responses request; OpenAI's standard API abuse-monitoring logs may still retain prompts and responses for up to 30 days. Do not paste passwords, one-time codes, recovery codes, payment-card details, or confidential data. The AI can make mistakes; verify high-impact advice with official sources. Current-events questions trigger web search, but the feature is not a substitute for a live threat-intelligence feed.

The browser-based device check remains sample data. This assistant cannot scan the user's phone or determine that a device is compromised.
