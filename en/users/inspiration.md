# Inspiration, Explorer and Clone

## Inspiration rail

The rail appears at the top of Easy and Pro modes. Starter sees up to 9 inspirations; eligible plans see up to 24 by default. It also contains My creatives, Favorites and Winners. Favorites and Winners are still coming soon.

The V2 selection is called **“Ideas to test for your brand.”** Its first 12 positions combine four ads with the strongest evidence, four highly relevant ideas, two new ads and two exploration slots. The same advertiser can occupy at most two of these positions.

Rollout is progressive: V2 first runs in parallel without changing the visible order, then activates on the rail before expanding to Explorer, Competitors and Search.

## Explorer

Explorer reads a shared curated pool without starting a new scrape. Filter by category, FR/US market and creative format. A “Top X% by reach” label can appear when EU reach is available and the comparable cohort contains at least 30 ads.

Ranking starts with the exact market, category and format. If fewer than 12 eligible ads remain, CreatAds widens format, category and then market, and discloses that expansion above the results. V2 uses a complete candidate cohort independent from the old V1 score.

## Competitors and search

Brand analysis can propose **candidates**. They stay labelled “Needs review”: an AI suggestion is not automatically a competitor.

From Inspiration, you can confirm, reject or add a brand manually. Manual add accepts a name, domain, Meta page and aliases. It creates a candidate to review and never starts a collection on its own.

After confirmation, “See ads” opens a preflight with the market, cache freshness, watch quota and Meta source. A fresh cache opens immediately with no extra cost or consent. Otherwise, CreatAds starts monitoring only after the explicit “Start monitoring” action.

Onboarding may show detected candidates, but it routes validation and the preflight to Inspiration. It does not start an Apify scrape.

Manual search is available on the Ads Library page for eligible plans. Valid cache hits are free; a new search consumes one Ads Library search. Failed searches are refunded, while a duplicate in-progress collection joins the existing run instead of starting another one.

## Ranking and evidence labels

Ranking V2 combines four dimensions: observable public performance signals, relevance to your brand, freshness and usability as an inspiration. Confidence remains separate from ranking, so missing data never boosts the remaining signals.

CreatAds does not label competitor ads as “Winners.” Depending on the available evidence, a card can display **Strong signal**, **Top X% by reach**, **Running for N days**, **New**, **Watch** or **Limited data**. These labels are never ROAS, CTR, CPA or revenue measurements.

The ad detail explains what CreatAds observes, why the ad is relevant to your brand and which evidence is still missing. You can save it, hide it or use it as inspiration.

Strong actions such as saving, using and then downloading a generated creative gradually improve feed relevance. **Reset my preferences** clears saved or hidden ads and makes future ranking ignore older behavioral history.

## Clone

1. Select the wand on an ad card.
2. CreatAds rehosts the asset through `save-template`.
3. Pro Mode can analyze the structure and propose CTA/offer text.
4. The generator switches to Reproduce.
5. The source brand is excluded from the final prompt.

Clone reuses structure and hierarchy, but the result is a new generation and is not guaranteed to be pixel-identical.

Opening, saving or prefilling an inspiration does not consume generation credits. Credits are calculated only when generation starts.

Inspiration, Explorer, Search and Clone are not exposed through the public API, SDK, CLI or MCP.
