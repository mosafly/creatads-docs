# Organic carousels

Open **Create → Organic carousels** (`/carousels`) to prepare a sequence of slides for Instagram or TikTok. Choose **Tips & storytelling** or **Product showcase**.

## Start from an observed structure

In **Inspiration → Carousels**, prepare an account watch, a search or an Instagram/TikTok post link. Preparation is free and inactive by default. A one-off collection or recurring activation requires approval of the displayed provider budget. Monitoring depends on a hosted worker and continues with your browser closed; its status is displayed in the interface.

Sources retain every slide in order, the author, link and dated observations. Unavailable metrics stay unavailable. A multiplier requires at least five other posts from the same account, platform and format, measured at a comparable post age. Importing an old post today does not reconstruct its historical 24-hour counters.

AI analysis describes each photo slide, its role, the narrative and hypotheses to test. Decks containing video remain labelled as mixed sources and are not presented as fully analyzed. Patterns and trends show sample counts and confidence without promising results.

**Use this structure** opens the generator with the saved analysis. Provide your own topic and Brand Kit: generation adapts slide roles and composition with original copy and prompts. It does not scrape the source again or reuse its photos. You can remove the reference in the editor.

Monitoring and analysis fees are separate from generation credits. A collection reserves at most **USD 0.15 on Instagram** or **USD 0.50 on TikTok**. TikTok's cap respects the provider's launch minimum; the reserved amount is not the actual cost of each collection. Analysis reserves **USD 0.07 for 2–8 slides**, then ordered batches and synthesis for longer decks, up to **USD 0.42 for 35 slides**. Reported actual costs are retained; an unknown cost remains reserved and is not shown as zero. An uncertain start pauses monitoring and is never resubmitted automatically. Availability requires deployment of the intelligence interface and backend on your instance.

The niche filter uses tags entered in your watches. It selects saved sources, structures and trends without another collection. Creator baselines retain their full comparable history. Activating an already imported post starts a fresh observation; follow-ups at 24 hours, 72 hours and 7 days start from the first actual observation in that monitoring period.

## Prepare your content

Choose a platform, content type and a topic of at least 10 characters. Add your audience, tone, final action and French or English content language. You can use Brand Kit context. For a product showcase, provide verified specifications and benefits; an optional product photo can guide AI visuals.

Choose **3 to 10 slides**, then **Generate outline · 1 credit**. CreatAds prepares a hook, a sequence, a conclusion, a caption and up to five hashtags. **Start without AI** opens blank slides without consuming credits.

## Edit the slides

Each title and body is editable. Add, delete or reorder slides within the 3-to-10 limit. AI visuals are optional: **Create visual · 1 credit** generates only the selected slide's image. Copy is added by the layout and remains editable.

Photos use **GPT Image 2.5 Flare at high quality**. The visual description is sent together with the slide's current title and body to clarify its meaning. Describe a concrete subject, action and setting. New outlines suggest photographic scenes relevant to each slide. Existing descriptions stay editable: an explicitly requested illustration retains that style. Preview and export retain the whole photo in the visual area without cropping its edges. Brand name and color belong to the layout; no additional logo is composited onto the photo.

Customize the displayed name, accent color, and light or dark style. Available image ratios are:

- Square `1:1`: 1080 × 1080 px.
- Portrait `4:5`: 1080 × 1350 px, the initial Instagram selection.
- Vertical `9:16`: 1080 × 1920 px, the initial TikTok selection.

These are the formats offered by CreatAds. Changing the platform updates the initial ratio; you can change it afterward. All exported slides use the same ratio.

## Save and export

After outline generation, the complete carousel is saved automatically to **My creatives** (`/library`). Find it in **All**, **Generated** or the **Carousels** filter, with its preview, platform and slide count. Open its card to continue editing, or use its export action to download the complete ZIP. Carousels saved by agents through our API/MCP appear in the same workspace collection.

Generated visuals are added to the document automatically. Use **Save to workspace** to keep manual edits. A newer cloud version is never overwritten automatically. **Find my carousels** remains available in the editor. Opening another carousel asks for confirmation before replacing a different draft.

One working draft is also saved **on this device**, separately for each account and workspace, and cleared on sign-out. If cloud saving fails, the result stays in this draft and a message invites you to retry saving without generating again. Complete drafts created before the My creatives integration are recovered when this library is opened.

The ZIP export contains:

- numbered PNG images in order;
- `caption.txt`, with caption and hashtags;
- `carousel.json`, the editable source to keep and reimport.

Complete every slide's title and copy before exporting. **Import source (.json)** restores a `carousel.json` file. Replacing an existing draft requires confirmation.

Copy the caption and upload the PNG files in order on your chosen platform. This feature prepares files; it does not automatically publish the post.

## Credits

Outline generation includes titles, bodies, caption, hashtags and **all visual prompts**, generated together by Mistral Small 4 on OpenRouter. There is no separate text call per prompt. When OpenRouter reports the actual cost, the editor displays the latest outline's provider cost in USD, retained in this device's working draft. This amount is informational and included in the CreatAds credit. An older outline without usage data does not display an invented cost.

An outline with its caption costs **1 credit**, regardless of the selected slide count. Each optional AI visual costs **1 additional credit**. Editing copy, reordering slides, starting without AI, and exporting do not consume credits. The cost is shown before each generation.

An agent can write the copy directly and save the carousel without calling CreatAds outline generation: **0 CreatAds credits** for copy, edits and export. The agent provider's own fees remain separate. See the [Agent First workflow](/en/developers/carousels-agent-first).

If outline generation fails, your existing draft is preserved. Invalid AI responses are not billed. When an image request has an uncertain outcome, check **My creatives** before requesting another visual.
