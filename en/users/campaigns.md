# Pro Mode

Pro Mode (`/campaigns`) creates multi-format, multi-angle batches from one configuration.

Select reference/product images, ratios, modes, angles, optional scene presets, volume, language, Brand Kit, CTA and offer.

```text
ratios x scenes/modes x angles x volume
```

When at least one preset is active, the volume multiplier becomes 1 because each preset already represents a distinct scene. Above 60 images, CreatAds requests confirmation; this is a safety prompt, not a hard limit.

## Ratios and modes

Pro Mode ratios: `1:1`, `4:5`, `9:16`, `16:9`.

| Family | Modes |
|---|---|
| Styles | Studio, Lifestyle, UGC |
| Layouts | Before/After, Feature Callout, Testimonial |
| Ad formats | Us vs Them, Anti-marketing, Static UGC, Testimonial, Limited Offer |
| Transformations | Reproduce, Reformat, Edit |

Testimonial appears in two UI families but maps to the same `testimonial-overlay` identifier. Pro Mode supports 13 unique mode identifiers.

## Execution and recovery

- Four images are generated concurrently.
- Progress is stored in the browser.
- A batch remains recoverable for about ten minutes.
- After reload, a batch inactive for roughly one minute can be resumed.

Recovery depends on `localStorage` and is not guaranteed after browser storage cleanup, browser changes or TTL expiry.

Each cell in the cartesian product produces one image and consumes one credit.
