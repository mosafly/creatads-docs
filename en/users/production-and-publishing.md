# Production and publishing

## Library

`/library` contains the active workspace's generated images, references, products and templates. You can upload, preview, navigate the lightbox by keyboard, download and delete. The former `/templates` route redirects to the Library's Templates section.

## Creative analysis

`/analyze` uploads a creative and returns an overall score, Meta-Ready status, commented category scores, strengths, weaknesses and recommendations. Results are saved to history; the image can become a template or a Pro Mode reference.

Analysis does not consume image credits. Scores are heuristics and do not predict ROAS, CPA or real campaign performance.

## Board and calendar

- `/campaigns/board` groups campaigns into Draft, Generating and Complete columns. It is currently a tracking view, not an editable drag-and-drop Kanban.
- `/campaigns/calendar` stores a campaign's schedule and platforms in the database.
- Manual creative placements and notes remain in `localStorage`, so they do not synchronize across browsers or devices.

## Meta Ads

`/campaigns/meta-ads` shows completed campaigns only. Load creatives, prepare copy and targeting with AI, review headline/body/description/CTA/offer/URL/budget, select images, choose a Meta strategy and objective, then publish. Available insights can be read after publishing.

The page can also create selected Meta audiences. It requires server-side Meta credentials and an eligible plan: Beta, Founder, Growth, Agency or Admin in the current UI feature table.

CreatAds does not guarantee Meta approval or performance. Verify budgets, content, targeting and final status in Meta Ads Manager.
