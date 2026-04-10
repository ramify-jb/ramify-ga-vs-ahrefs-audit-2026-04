# Enquête GA4 vs Ahrefs Web Analytics

## Résumé exécutif

Le décalage `Ahrefs Web Analytics vs Google Analytics` est réel, mais le diagnostic solide n'est **pas** “Ahrefs monte pendant que GA s'effondre”.

La lecture la plus robuste est :

1. **Vous ne comparez pas toujours la même métrique.**
   Ahrefs `visitors` n'est pas l'équivalent de GA4 `totalUsers`. La comparaison la plus saine au niveau site est plutôt `Ahrefs visits` vs `GA sessions`.

2. **Le screenshot Ahrefs `www` montre bien une légère hausse brute mois à mois, mais elle est surtout calendaire.**
   En `www all traffic`, `1–31 Mar 2026` vs `1–28 Feb 2026` donne une légère hausse brute, mais une fois ramené au jour :
   - Ahrefs `www visits / jour` : `5 649` -> `5 274` (`-6,6 %`)
   - Ahrefs `www visitors / jour` : `5 295` -> `4 904` (`-7,4 %`)

3. **Le point utile n'est pas de savoir si le SEO a baissé, mais que GA4 décroche beaucoup plus que les benchmarks externes.**
   Sur `www.ramify.fr` :
   - GA4 `Organic Search sessions` : `77 722` -> `48 149` (`-38,0 %`)
   - GA4 `Direct sessions` : `27 540` -> `10 714` (`-61,1 %`)
   - GA4 `Referral sessions` : `9 466` -> `5 412` (`-42,8 %`)
   - et le signal anormal existe aussi au niveau des `pageviews`, pas seulement des `sessions`

4. **Je ne trouve pas de preuve d'un bug fatal live, ni d'un “jour de rupture” unique.**
   Le live actuel envoie bien des hits GA4 sur `www`, en consent `denied` comme en consent `granted`, et le daily cut ne montre pas de cliff net au `2026-03-01` ou au `2026-03-02`.

En synthèse :

- Ahrefs `www` est **en légère hausse en brut mois à mois**, mais **pas** une fois normalisé au jour
- GA4 décroche **beaucoup plus** que les benchmarks externes
- le meilleur diagnostic à ce stade est une **dégradation historique de mesure GA4 sur le trafic public `www`**, surtout sur `Organic`, `Direct` et `Referral`
- je n'ai pas assez d'éléments pour l'attribuer avec certitude à un tag totalement cassé, à `app.ramify.fr`, ou à un unique déploiement précis

## Ce que j'ai vérifié

- Pulls GA4 sur la propriété utile : `312503061`
- Pulls Ahrefs Web Analytics sur le projet : `3895257`
- Pulls GSC sur `sc-domain:ramify.fr`
- Séries journalières GA4 et GSC sur février-mars 2026
- Split GA4 par `hostName`, `channel`, `browser` et `landingPage`
- Inspection live de `www.ramify.fr`
- Inspection du HTML + bundle de `app.ramify.fr`
- Probe runtime live sur `/`, `/private-equity` et `/epargne/livret-a-plafond`
- Comparaison de snapshots janvier / mars / avril sur quelques pages publiques ; les raw HTML crawlés conservent le markup `fs-consent`, mais pas de snippet historique GTM/GA exploitable pour prouver un changement de tagging

## Constat 1 : `www` est le bon périmètre pour le SEO public

En mars 2026, GSC par hostname donne :

| Host | Clics |
| --- | ---: |
| `www.ramify.fr` | `40 071` |
| `ramify.fr` | `200` |
| autres hosts | ~`0` |

Pour comparer le SEO entre outils, le bon périmètre est donc :

- `GSC clicks on www`
- `Ahrefs search`
- `GA4 organic on www`

Pas le property global GA4.

## Constat 2 : GSC et Ahrefs `search` sont des benchmarks externes cohérents

En mars 2026 :

| Source | KPI | Valeur |
| --- | --- | ---: |
| GSC | clics `www.ramify.fr` | `40 071` |
| Ahrefs | `search` visitors | `39 947` |

Sur juillet 2025 -> mars 2026, la corrélation `Ahrefs search` vs `GSC clicks` est de `0,998`.

Conclusion :

- Ahrefs `search` n'est pas hors-sol sur le SEO public
- GSC et Ahrefs `search` peuvent servir de baseline externe pour évaluer si GA4 diverge trop

## Constat 3 : le premier gros écart vient de la mauvaise équivalence métrique

Ahrefs :

- `visitors` = visiteurs uniques par jour agrégés
- `visits` = visites

GA4 :

- `totalUsers` = utilisateurs dédupliqués sur la période
- `sessions` = sessions

Mars 2026 :

| Source | Métrique | Valeur |
| --- | --- | ---: |
| Ahrefs | `visitors` | `197 848` |
| Ahrefs | `visits` | `219 306` |
| GA4 | `totalUsers` | `155 829` |
| GA4 | `sessions` | `229 021` |

Donc :

- `Ahrefs visitors` vs `GA users` crée mécaniquement un gros gap
- `Ahrefs visits` vs `GA sessions` est beaucoup plus propre

## Constat 4 : même filtré sur `www`, la légère hausse Ahrefs brute est surtout un effet calendrier

Le screenshot Ahrefs compare :

- `1–28 Feb 2026`
- `1–31 Mar 2026`

Avec filtre `page_domain = www.ramify.fr` :

| Période | Pageviews | Visitors | Visits |
| --- | ---: | ---: | ---: |
| février 2026 | `209 005` | `148 268` | `158 161` |
| mars 2026 | `238 092` | `152 015` | `163 511` |

En brut mois à mois, Ahrefs `www` montre donc bien une légère hausse :

- `visitors` : `148 268 -> 152 015`
- `visits` : `158 161 -> 163 511`

Mais en moyenne par jour :

| KPI | Février / jour | Mars / jour | Variation / jour |
| --- | ---: | ---: | ---: |
| Ahrefs `visitors` `www` | `5 295` | `4 904` | `-7,4 %` |
| Ahrefs `visits` `www` | `5 649` | `5 274` | `-6,6 %` |

Donc la contradiction utile n'est pas :

- “Ahrefs monte, GA chute”

Mais plutôt :

- “Ahrefs `www` est légèrement up en brut, légèrement down par jour, et GA `www` baisse beaucoup plus”

## Constat 5 : le paid ne mappe pas proprement entre Ahrefs et GA4

Février -> mars 2026 :

- Ahrefs `search/paid` : `61 751` -> `76 249` (`+23,5 %`)
- GA4 `Cross-network + Paid Search + Paid Social + Paid Other` : `136 101` -> `121 143` (`-11,0 %`)

Conclusion :

- `search/paid` Ahrefs n'est pas comparable 1:1 à un seul canal GA4
- le total site peut diverger fortement juste à cause de cette différence de taxonomie

## Constat 6 : sur `www`, GA4 diverge fortement des benchmarks externes

Sur `www.ramify.fr`, février -> mars :

| KPI | Février | Mars | Variation |
| --- | ---: | ---: | ---: |
| GSC `www clicks` | `50 851` | `40 071` | `-21,2 %` |
| GA4 `www google organic sessions` | `73 413` | `45 805` | `-37,6 %` |
| GA4 `www organic sessions` | `77 722` | `48 149` | `-38,0 %` |

Donc même en restant sur :

- `www`
- `google`
- `organic`
- `sessions`

GA4 décroche beaucoup plus que GSC.

Ce n'est pas juste un problème `users` vs `visitors`.

## Constat 7 : ce n'est pas seulement quelques pages, le pattern existe sur un vrai volume

J'ai comparé page par page :

- `GA4 landingPage google organic sessions`
- vs `GSC page clicks`

Parmi les pages avec au moins `500` clics GSC sur février ou mars :

- `23` pages entrent dans le périmètre
- `12` pages (`52,2 %`) ont une baisse GA **au moins 20 points plus forte** que la baisse GSC
- ces pages représentent `35,0 %` des sessions GA de février sur ce sous-ensemble

Exemples marquants :

| Page | GA4 | GSC |
| --- | --- | --- |
| `/epargne/livret-a-plafond` | `1944 -> 1268` (`-34,8 %`) | `1868 -> 2276` (`+21,8 %`) |
| `/epargne/epargne-moyenne-des-francais` | `2407 -> 1560` (`-35,2 %`) | `2327 -> 2269` (`-2,5 %`) |
| `/assurance-vie/perte-argent` | `978 -> 599` (`-38,8 %`) | `1140 -> 1237` (`+8,5 %`) |
| `/epargne/combien-avoir-de-cote-a-50-ans` | `970 -> 483` (`-50,2 %`) | `1200 -> 1312` (`+9,3 %`) |

Conclusion :

- il existe un sous-ensemble important de pages où **GA se dégrade bien plus que GSC**
- cela pointe vers une **sous-mesure GA4 sur le trafic public** au-delà de la seule variation observée dans GSC

## Constat 8 : la dégradation ne ressemble pas à un seul jour de panne

Sur le ratio `GA www google organic sessions / GSC www clicks` :

| Période | Ratio moyen |
| --- | ---: |
| `2026-02-01` -> `2026-02-14` | `1,674` |
| `2026-02-15` -> `2026-02-28` | `1,217` |
| `2026-03-01` -> `2026-03-14` | `1,179` |
| `2026-03-15` -> `2026-03-31` | `1,129` |

Lecture :

- la dégradation commence **déjà en seconde moitié de février**
- elle continue en mars
- je ne vois pas de cliff propre au `2026-03-01` ou au `2026-03-02`

Donc je ne peux pas défendre un scénario du type :

- “un tag est mort d'un coup le 2 mars”

## Constat 8 bis : ce n'est pas juste un problème de reclassement `google / organic`

Sur plusieurs pages où GSC reste stable ou monte, la baisse n'est pas limitée à `google / organic`.

Exemples :

| Page | GSC | GA4 `landingPage google / organic sessions` | GA4 `landingPage all channels sessions` |
| --- | --- | --- | --- |
| `/epargne/livret-a-plafond` | `1868 -> 2276` (`+21,8 %`) | `1944 -> 1268` (`-34,8 %`) | `4071 -> 3165` (`-22,3 %`) |
| `/assurance-vie/perte-argent` | `1140 -> 1237` (`+8,5 %`) | `978 -> 599` (`-38,8 %`) | `1786 -> 1342` (`-24,9 %`) |
| `/epargne/combien-avoir-de-cote-a-50-ans` | `1200 -> 1312` (`+9,3 %`) | `970 -> 483` (`-50,2 %`) | `1942 -> 1358` (`-30,1 %`) |

Conclusion :

- ce n'est **pas seulement** un reclassement de `google / organic` vers un autre canal
- sur une partie des pages publiques, GA4 mesure aussi moins de **landing sessions totales**
- cela renforce la lecture “problème de mesure ou de sessionisation côté public site”

## Constat 8 ter : l'écart existe aussi au niveau `page_view`, pas seulement au niveau `landing session`

J'ai repris le même sous-ensemble de grosses pages, mais en remplaçant :

- `GA4 landingPage google / organic sessions`

par :

- `GA4 pagePath google / organic screenPageViews`

Sur les pages avec au moins `500` clics GSC en février ou mars :

- `23` pages entrent dans le périmètre
- `20` pages (`87,0 %`) ont une baisse de `pageviews` GA **au moins 20 points plus forte** que la baisse GSC
- ces pages représentent `88,6 %` des `pageviews` GA organiques de février sur ce sous-ensemble

Exemples :

| Page | GA4 `organic pageviews` | GSC |
| --- | --- | --- |
| `/epargne/livret-a-plafond` | `2523 -> 1330` (`-47,3 %`) | `1868 -> 2276` (`+21,8 %`) |
| `/epargne/epargne-moyenne-des-francais` | `3245 -> 1645` (`-49,3 %`) | `2327 -> 2269` (`-2,5 %`) |
| `/assurance-vie/perte-argent` | `1196 -> 626` (`-47,7 %`) | `1140 -> 1237` (`+8,5 %`) |
| `/epargne/combien-avoir-de-cote-a-50-ans` | `1182 -> 501` (`-57,6 %`) | `1200 -> 1312` (`+9,3 %`) |

Lecture :

- le trou n'est pas limité aux `landing sessions`
- il apparaît aussi sur les `pageviews` GA rapportées
- il est réparti sur plusieurs sections (`/`, `epargne`, `assurance-vie`, `pea`, `outils`, `produits`, `gestion-de-patrimoine`)
- cela rend **moins probable** une explication purement centrée sur la sessionisation, le reclassement de canal ou la landing attribution

Autre signal important : le ratio sitewide `GA organic pageviews / GSC clicks` se casse lui aussi dès la deuxième moitié de février :

| Période | Ratio moyen |
| --- | ---: |
| `2026-02-01` -> `2026-02-14` | `2,919` |
| `2026-02-15` -> `2026-02-28` | `1,576` |
| `2026-03-01` -> `2026-03-14` | `1,452` |
| `2026-03-15` -> `2026-03-31` | `1,422` |

Donc la dégradation commence :

- avant mars
- et au niveau `page_view` lui-même, pas seulement au niveau `session`

## Constat 9 : le problème n'est pas limité à un seul navigateur

Sur `www` tous canaux :

- Chrome : `132 647` -> `94 452` (`-28,8 %`)
- Safari : `57 436` -> `37 775` (`-34,2 %`)
- Firefox : `11 567` -> `7 233` (`-37,5 %`)
- Edge : `11 813` -> `7 801` (`-33,9 %`)

Sur `www` `google / organic` :

- Chrome : `43 361` -> `27 866` (`-35,7 %`)
- Safari : `20 284` -> `11 982` (`-40,9 %`)
- Firefox : `4 017` -> `2 257` (`-43,8 %`)
- Edge : `3 272` -> `1 870` (`-42,9 %`)

Conclusion :

- la baisse est **broad-based**
- je ne vois pas de signature “Safari only” ou “Firefox only”
- ça rend moins probable un bug strictement lié à une seule famille de navigateurs

Nuance utile :

- sur `www google / organic`, la baisse est un peu plus forte sur mobile (`40 544 -> 23 220`, `-42,7 %`) que sur desktop (`31 801 -> 21 463`, `-32,5 %`)
- mais elle existe sur les deux, donc je ne la lis pas comme un bug mobile-only de CMP ou de bannière

## Constat 10 : le live actuel n'est pas en panne

Le probe runtime actuel sur `www` montre :

- un `page_view` GA4 en consent `denied`
- un `page_view` GA4 en consent `granted`
- des events GA4 custom sur page article

Pages testées :

- `/`
- `/private-equity`
- `/epargne/livret-a-plafond`

Donc je ne vois pas aujourd'hui de bug fatal du type :

- “les visiteurs consentis ne sont plus du tout trackés”

## Constat 10 bis : le problème ne ressemble pas à une simple inflation de sessions en février

Si février avait été principalement “gonflé” par une sessionisation anormale, on s'attendrait surtout à voir :

- beaucoup plus de sessions
- mais une profondeur par session similaire ou meilleure en mars

Or sur `www`, février -> mars :

| Segment | `screenPageViews / session` | `engagedSessions / session` |
| --- | --- | --- |
| Tous canaux | `1,497 -> 1,186` | `0,618 -> 0,451` |
| `google / organic` | `1,578 -> 1,250` | `0,624 -> 0,492` |

Lecture :

- les sessions baissent
- les pages vues par session baissent aussi
- l'engagement par session baisse aussi

Cela ne prouve pas à lui seul un bug de tracking, mais ce pattern est **moins compatible** avec “février était juste artificiellement gonflé en sessions” qu'avec :

- une baisse réelle de qualité / profondeur du trafic
- et/ou une perte de mesure sur des hits ultérieurs dans le parcours

## Constat 11 : `app.` n'est pas le meilleur suspect

L'app charge :

- un GTM dédié `GTM-5PFR7VZ`
- le script Ahrefs via GTM
- un tag GA4 `page_view`
- des références `gtm.historyChange`

Je ne vois donc pas de preuve forte que le coeur du problème soit :

- “la SPA app ne tracke pas bien dans GA4”

Le vrai signal anormal est surtout côté `www`.

## Ce que j'en conclus

### Ce qui est solide

1. **Le problème principal n'est pas `app.`.**
   L'app est instrumentée et ce n'est pas là que l'écart devient le plus anormal.

2. **GA4 raconte une variation beaucoup plus défavorable que GSC / Ahrefs `search` sur une part importante du site public.**
   C'est visible même en regardant `www + google + organic + sessions + landingPage`, et cela reste vrai au niveau `pageviews`.

3. **Je n'ai pas de preuve d'un tag entièrement mort ni d'un unique jour de rupture.**
   Le problème ressemble plus à une **dégradation de mesure** qu'à une panne binaire.

### Meilleure hypothèse restante

La meilleure hypothèse à ce stade est :

- une **sous-mesure historique de GA4 sur le trafic public `www`**
- probablement dans la chaîne **CMP / consent / GTM / reporting des hits / session attribution**
- déjà en train de se dégrader en seconde moitié de février
- et toujours visible en mars

Je ne peux pas prouver, avec les données à disposition, si cette dégradation vient précisément :

- d'un changement de consentement
- d'un réglage GTM
- d'un changement d'attribution/sessionisation
- ou d'un mélange de ces facteurs

Mais je peux dire que :

- **la piste “simple différence de métrique” ne suffit pas**
- **la variation observée dans GSC / Ahrefs `search` ne suffit pas** à expliquer l'ampleur de l'écart GA4
- **la piste “simple problème de sessionisation” ne suffit pas non plus**
- **la piste “bug fatal live aujourd'hui” n'est pas supportée**

## Recommandation de lecture

### Pour le benchmark SEO public

Source de vérité :

- **GSC `www.ramify.fr` clicks**

Source de contrôle :

- **Ahrefs `search`**

Usage GA4 :

- comportement
- conversion
- pages d'atterrissage
- mais en gardant en tête qu'il existe probablement un **biais de sous-mesure** sur une partie du trafic public

### Pour le trafic global

Si tu compares Ahrefs et GA4 :

- compare plutôt **Ahrefs `visits`** à **GA4 `sessions`**
- pas **Ahrefs `visitors`** à **GA4 `totalUsers`**

## Recommandations client

### Immédiat

1. **Auditer l'historique des changements entre le 15 et le 28 février 2026.**
   À vérifier en priorité :
   - versions GTM publiées
   - changements CMP / Finsweet consent
   - changements de code `head` / scripts dans Webflow
   - éventuels changements de règles de consentement ou de déclenchement GA4
   - toute modification des paramètres du tag de configuration GA4

### Dans les 1 à 2 semaines

2. **Faire un QA de tracking complet sur les templates qui pèsent le plus.**
   Priorité sur :
   - home `/`
   - articles `epargne`
   - articles `assurance-vie`
   - pages `pea`
   - `outils`
   Vérifier sur desktop et mobile :
   - première page vue
   - navigation interne
   - consent refusé / accepté
   - présence des hits `page_view`
   - présence des hits `session_start`
   - présence des hits de navigation suivants
   - hostname, page path et source / medium remontés côté GA4

3. **Prioriser l'investigation sur les pages les plus anormales.**
   Commencer par :
   - `/epargne/livret-a-plafond`
   - `/epargne/epargne-moyenne-des-francais`
   - `/assurance-vie/perte-argent`
   - `/epargne/combien-avoir-de-cote-a-50-ans`
   Ce sont les meilleures pages de test pour vérifier si le problème est toujours observable en runtime.

4. **Passer chaque scénario dans Tag Assistant + DevTools réseau.**
   Pour chaque template prioritaire, comparer :
   - consent refusé
   - consent accepté
   - première arrivée depuis Google
   - navigation interne vers une deuxième page
   - refresh de page
   L'objectif est d'identifier précisément où se perd la mesure :
   - avant le `page_view`
   - après le `page_view`
   - au niveau `session_start`
   - ou au moment du changement de page

5. **Vérifier s'il existe un problème de firing conditionnel ou partiel dans GTM.**
   Contrôler notamment :
   - triggers du tag de config GA4
   - dépendances au consent mode
   - exceptions / blocking triggers
   - différences entre page load, SPA navigation et history change
   - éventuels tags ou scripts qui remplacent / retardent le chargement normal

### Pour corriger et sécuriser

6. **Si possible, activer ou exploiter un export brut GA4 / logs.**
   Le meilleur moyen de sortir du doute est de pouvoir comparer :
   - `page_view`
   - `session_start`
   - hostname
   - page path
   - consent state
   dans la donnée brute, pas seulement dans l'UI GA4.

7. **Après correction, suivre explicitement un plan de validation.**
   Le correctif ne doit pas être considéré comme validé tant que l'équipe n'a pas confirmé :
   - hits GA4 présents sur les pages prioritaires en consent refusé et accepté
   - comportement cohérent sur première page puis navigation interne
   - absence de décrochement entre `page_view` et `session_start`
   - normalisation progressive du ratio `GA4 / GSC` sur les pages suivies

8. **Mettre en place un contrôle d'alerte simple pour éviter une rechute silencieuse.**
   Le plus utile est un check hebdomadaire sur :
   - `GA4 www google / organic sessions / GSC www clicks`
   - `GA4 www organic pageviews / GSC www clicks`
   Si ces ratios se recassent brutalement, l'équipe saura immédiatement qu'il faut réouvrir une investigation tracking.

## Fichiers

- Tableau mensuel : `monthly_comparison.csv`
- Données consolidées : `analysis.json`
- Synthèse consolidée : `summary.json`
- Daily cut autour de février -> mars : `daily_www_transition.csv`
- Daily cut `www` organic pageviews vs GSC : `daily_www_organic_pageviews_transition.csv`
- Ratios journaliers agrégés : `daily_www_organic_pageview_ratios.json`
- Analyse page par page GA vs GSC : `page_gap_analysis.csv`
- Analyse page par page avec `pageviews` : `page_gap_analysis_with_pageviews.csv`
- Stats agrégées sur l'écart `pageviews` : `pageview_gap_stats.json`
- Probe runtime : `runtime_probe_output.json`
- Probe runtime visiteurs consentis / non consentis : `returning_user_probe.json`
- Note de lecture runtime : `runtime_probe_notes.md`

## Sources

- Ahrefs Web Analytics overview: https://help.ahrefs.com/en/articles/10247870-about-ahrefs-web-analytics
- Google consent mode: https://developers.google.com/tag-platform/security/guides/consent
- Google GA4 session definition: https://support.google.com/analytics/answer/12798876
