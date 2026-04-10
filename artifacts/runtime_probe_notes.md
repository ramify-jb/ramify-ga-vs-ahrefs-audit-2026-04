# Notes probe runtime `www.ramify.fr`

Pages testées :

- `https://www.ramify.fr/`
- `https://www.ramify.fr/private-equity`
- `https://www.ramify.fr/epargne/livret-a-plafond`

## Correction importante

Une première lecture était trompeuse parce qu'elle ne regardait que `google-analytics.com`.

En réalité :

- les hits GA4 en mode consent refusé partent vers `region1.google-analytics.com/g/collect`
- les hits GA4 en mode consent accepté partent vers `region1.analytics.google.com/g/collect`

## Ce qui a été vu

### Visiteur anonyme, consentement non accordé

Sur les 3 pages :

- `GTM-T66PLCW` charge
- `analytics.ahrefs.com/analytics.js` charge
- `gtag/js?id=G-FRCF1F3YBQ` charge
- un hit `GA4 page_view` part bien avec :
  - `tid=G-FRCF1F3YBQ`
  - `gcs=G100`
  - `pscdl=denied`

### Visiteur revenant, consentement déjà accordé

Avec cookies de consentement acceptés déjà présents :

- `fs-consent-analytics_storage=true`
- `fs-consent-ad_storage=true`
- `_ga`
- `_ga_FRCF1F3YBQ`

Sur les 3 pages :

- `GTM-T66PLCW` charge
- `analytics.ahrefs.com/analytics.js` charge
- `gtag/js?id=G-FRCF1F3YBQ` charge
- un hit `GA4 page_view` part bien avec :
  - `tid=G-FRCF1F3YBQ`
  - `gcs=G111`
  - `pscdl=noapi`

Et sur l'article `livret-a-plafond`, un event custom GA4 est aussi vu :

- `en=visit_content`
- `tid=G-FRCF1F3YBQ`

## Lecture

Le live actuel ne montre donc pas un bug fatal du type :

- “les visiteurs ayant accepté les cookies ne sont plus trackés par GA4”

En revanche, le setup GA4 reste plus complexe et plus fragile qu'Ahrefs :

- GTM
- consent mode
- plusieurs endpoints Google (`googletagmanager.com`, `analytics.google.com`, `google.com`, `googleadservices.com`)

Cela laisse comme causes plausibles du gap :

- différences de métriques (`visitors` vs `users/sessions`)
- consentement / modélisation GA4
- protections anti-tracking / ad blockers
- taxonomies channel différentes
- ou régression historique passée non datable avec le mirror
