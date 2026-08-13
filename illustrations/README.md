# Editorial illustrations

This repository follows the same `editorial-comic-v1` visual, file, markup, and
review contract documented in the Chinese blog. English images are generated
separately rather than localized from Chinese pixels.

- Style: confident ink, restrained flat colour, warm-grey paper, amber and
  muted-teal accents, and a lightly humorous editorial metaphor.
- Avoid readable in-image text, logos, trademarks, real book covers,
  recognizable public figures, and invented technical interfaces.
- Output is sRGB WebP at exactly `1536x864`, target `<= 200000` bytes and hard
  limit `250000` bytes.
- Files live under `source/images/ai/<slug>/`; manifests live at
  `illustrations/<slug>.yml`.
- Posts may reference only manifest entries whose `review_status` is
  `approved`.
- Candidate files remain under `review/`, outside the published `source/`
  tree, until the author approves them.

Hero markup:

```html
<figure class="editorial-illustration editorial-illustration--hero">
  <img src="/en/images/ai/post-slug/en-hero.webp" alt="Meaningful English alternative text" width="1536" height="864" decoding="async">
</figure>
```

Inline images additionally use `loading="lazy"`.
