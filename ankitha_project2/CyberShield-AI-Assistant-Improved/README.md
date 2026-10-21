# CyberShield AI — interactive preview

The website files are in `site/`. For a local static preview, open `site/index.html` in a modern browser; sign-in and the AI function require Netlify deployment and configured services.

## SMS message checker

The Ask CyberShield page includes a local SMS spam checker. It reads `site/sms-model.json` on the first check and runs the model in the browser. Pasted SMS text is not posted to the AI backend by this feature. Keep `sms-model.json` beside `index.html` in `site/`. To check locally, serve the `site/` folder through a local web server (for example, VS Code Live Server); opening `index.html` directly as a `file://` URL prevents the browser from fetching the model.

The model is trained from the provided SMS Spam Collection CSV by `backend/train_model.py`. Keep the original CSV outside the public website folder. Retrain the model locally and replace `sms-model.json` to publish a new model. The model is educational and may miss new or non-English scams; its result is not proof that a message is safe or malicious.

## Email/password sign-in

The app has a sign-in/create-account screen backed by Supabase Auth. Put the Supabase Project URL and public publishable key in `site/auth-config.js`. In Supabase Auth URL Configuration, set the deployed Netlify URL as the Site URL and add that exact origin to Redirect URLs. Email confirmation may be enabled, so new users may need to confirm their email before signing in.

Never place a Supabase `service_role` or secret key in `site/auth-config.js`. The AI function independently validates the Supabase access token. Static assets (including the SMS model file) remain publicly downloadable.

## Connected AI assistant

The Ask CyberShield chat now calls Gemini through the authenticated Netlify Function at `netlify/functions/cybershield-chat.mjs`. Configure `GEMINI_API_KEY`, `SUPABASE_URL`, and `SUPABASE_PUBLISHABLE_KEY` as Netlify environment variables, then deploy the full project so the function is included. The Gemini key must remain server-side. Free-tier quotas and terms apply; see [AI-ASSISTANT-SETUP.md](AI-ASSISTANT-SETUP.md) for setup and privacy details.

## What is implemented

- Beginner-friendly overview, device check, threat briefings, learning path, assistant, and privacy pages.
- Gemini-backed cybersecurity chat with recent-turn context, selectable explanation level, Supabase session verification, server-side API key storage, Google Search grounding for current questions, and visible source links when citations are returned.
- Concept cards with a simple definition, example, how it happens, warning signs, protection steps, response guidance, and technical explanation.
- Clear demo labels. The security score and device signals are sample data; the browser does not scan or access a phone.
- Official CERT-In and CISA advisory links for users to check primary sources.

## Production work still required

This is a product prototype, not a deployed mobile security service. It does not include a Flutter Android/iOS client, FastAPI service, PostgreSQL storage for application data, native device permission integrations, push notifications, or operational security controls. Configure Supabase Auth and the Gemini/Netlify Function environment before sign-in and the AI chat work. Verify AI advice and never treat it as a device forensic diagnosis.

To make device checks real, implement only platform-approved Android/iOS APIs, explain each permission before requesting it, and report unsupported signals honestly. Keep deterministic configuration checks separate from any LLM interpretation. Define backend retention and processing behavior before sending user data to a model.


## Profile and learning progress

After a Supabase sign-in, open the avatar/profile control to see the account email, completed module count, the next module, explanation-level settings, progress reset, and sign-out. Learning progress and the explanation level are saved in this browser under the signed-in Supabase user ID; they are not synced to other devices or stored in the project database.

The learning path contains nine modules. Only the next unfinished module is unlocked. Completing a module saves the progress, unlocks the following module, and opens it immediately. Completed modules remain available to revisit.

## Learning modules and knowledge checks
All nine modules can be opened from the learning path. Completing a lesson starts a three-question knowledge check. The score, answer review, and explanation are shown at the end; users can retake the quiz. Module completion and quiz results are saved locally in the browser. Resetting learning progress clears both completion and quiz results.

