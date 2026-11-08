# Studio agent: create through chat

**Studio** (`/studio`) brings together your conversation, creatives and carousel preview. This personal pilot requires the local companion connected to your Codex account and the same CreatAds account. Availability on an instance depends on deploying the Studio migrations, backend and frontend.

1. Select your brand in Studio. It determines the Brand Kit and accessible creatives.
2. Choose Instagram or TikTok, then Editorial or Product.
3. Describe your request and use the send arrow or Enter. Shift + Enter adds a line.
4. The agent creates the carousel and saves it to **My creatives**. Completed steps appear in chat.
5. Continue in the same conversation: “Shorten the title on slide 2”, “Use a dark style” or “Export it as a ZIP”.

The preview appears on the right. Select a slide to provide context for your request. Exports appear in chat with a download button and contain ordered PNGs, the caption and the editable document at the exported revision. Nothing is published to a social network.

## Reopen and resume

Conversations and carousels persist for the selected brand. Reopen a conversation from history after closing the page. **My creatives** also reopens a project in Studio. Switching brands changes the accessible conversations and creatives.

A disconnected browser does not automatically cancel ongoing work. Use **Resume request** to recover saved steps. Stopping a request prevents further work but preserves changes already saved. If another session edited the project, reopen its current version before a new request.

## Connection and scope

Inference uses your personal Codex subscription quota. There is no paid API-key fallback or promised fixed currency price per carousel. Keep the companion running for generation, previews and exports; history remains readable through the service when it is stopped. The browser's CreatAds account must match the companion's account.

This milestone creates text-led graphic carousels using Brand Kit colors and fonts. AI photos and logo layers are not included yet. This is a personal pilot, not a commercial ChatGPT connection for all SaaS users.

## New interface and Higgsfield video

Studio now offers rounded panels, compact navigation and light/dark themes. On mobile, **Conversation** and **Workspace** switch between chat and content. History, My creatives and Connections are available in the navigation rail.

Select **Video** or request a storyboard in chat. Codex prepares one to six shots using the Brand Kit. Request script edits in the conversation; each version stays saved there.

1. Review **Script & storyboard**.
2. Click **Estimate generation**. The companion requests pricing from Higgsfield for each shot’s exact parameters.
3. Review the USD amount, then click **Confirm and generate**. Quotes last five minutes. The agent cannot approve spending for you.
4. Keep Studio open to track clips and start subsequent shots in the approved batch. Reopening resumes tracking. **Stop subsequent shots** preserves completed clips; a clip already processing at Higgsfield continues.
5. Once all clips are ready, **Export edit** produces a ZIP containing a silent MP4, separate SRT subtitles, caption and storyboard. FFmpeg must be installed on the companion.

This integration uses **Kling 4.0 Flash** at 720p, from text, in 9:16, 16:9 or 1:1. Exact logos and products are not guaranteed. Voiceover, music, custom avatars and social publishing are not included in this first integration.

Storyboards persist in cloud conversations. Clips, quotes and generation states stay in the companion’s private client-scoped storage; videos appear in Studio’s **My creatives** on that computer. This is not a video library synced across devices yet.

Higgsfield uses an **API balance separate** from Codex and the Higgsfield website subscription. Set `HF_API_KEY_ID` and `HF_API_KEY_SECRET` in the companion environment or its private `work/creatads-v2/.env.local`, then restart it. Never paste keys in chat. Connections shows whether keys are configured; this does not verify their validity or the account balance.

Interrupted submissions recover with their original idempotency key. A failed generation is never automatically replaced with another paid request. A changed price blocks subsequent shots. Prepare a new version and review its quote. Production video generation requires configuration and live API-account validation; mocked provider tests do not establish it.
