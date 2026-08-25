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

`/campaigns/meta-ads` is the **Meta Analytics** page. Connect an ad account, then refresh the last seven days of data. Open a campaign to see its ads, spend, impressions, clicks, CTR, CPM and reach. An ad can be linked to a Creatads creative when its Meta ID is known.

The page is strictly read-only: it does not create or modify campaigns, audiences, ads, budgets or delivery statuses. It requires server-side Meta credentials and an eligible plan: Beta, Founder, Growth, Agency or Admin in the current UI feature table.

CreatAds does not guarantee Meta approval or performance. Always verify final data in Meta Ads Manager.
