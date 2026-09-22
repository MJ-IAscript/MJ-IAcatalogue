# MJ-IAcatalogue

Catalogue vitrine **cross-script** consommé par le module Courtier (NPC vitrine, présent dans
Vangelico et les futurs scripts MJ-IA). Ce repo est **public volontairement** : `catalogue.json`
est récupéré directement depuis le serveur FXServer de chaque acheteur via
`raw.githubusercontent.com` (aucune authentification possible côté script vendu).

Ne contient **aucun code** — uniquement de la donnée marketing (textes, liens). Éditer
`catalogue.json` directement ici (interface GitHub ou `git push`) met à jour la vitrine de tous les
acheteurs sous 24h max (`catalogueTtlSeconds` côté script), sans toucher au code ni republier de
version.

## Schéma

```jsonc
{
  "version": 1,
  // Liens sociaux (racine, optionnels) — masqués individuellement si absents.
  "discord_link": "https://discord.gg/...",
  "facebook_link": "https://facebook.com/...",
  // Actualités (racine, optionnel) — 3 premières entrées affichées, PAS triées par
  // published_at : l'ordre du tableau JSON est l'ordre d'affichage, à organiser à la main.
  "items": [
    {
      "type": "upcoming", // "upcoming" | "update" | "announcement"
      "title": "...",
      "teaser": "...",       // upcoming uniquement
      "body": "...",         // update / announcement uniquement
      "media_url": "https://...", // upcoming uniquement — lien externe (ex. bande-annonce)
      "link": "https://...",      // update / announcement uniquement — "En savoir plus"
      "expires_at": "2026-12-31T00:00:00Z" // ISO 8601, optionnel — entrée masquée une fois expirée
    }
  ],
  "scripts": [
    {
      "resourceName": "the_diamonds_never_die", // nom EXACT de la resource FXServer
      "label": "The Diamonds Never Die", // ou { "fr": "...", "en": "...", "es": "..." }
      "description": { "fr": "...", "en": "...", "es": "..." }, // ou chaîne simple
      "image": "html/img/xxx.png",            // optionnel, résolu via nui://<resourceName>/...
      "detailBackground": "html/img/xxx.png", // optionnel, même résolution, SI le script est installé
      "tebexUrl": "https://xxx.tebex.io/"
    }
  ]
}
```

`label`/`description` acceptent une chaîne simple (affichée identique dans toutes les langues) ou
une table `{fr, en, es}` résolue côté client selon `Config.SubLang` — toujours préférer la table.

`image`/`detailBackground` sont résolus par le client **du script concerné** via
`nui://<resourceName>/<chemin>` — le chemin est relatif à la RACINE de la resource FXServer
installée chez l'acheteur, pas à ce repo. Ces champs ne fonctionnent donc que pour un script déjà
installé chez le joueur qui consulte le catalogue ; sans installation, l'entrée reste affichable
(label/description/tebexUrl) mais sans image.

## Utilisation

Consommé par `Config.Courtier.catalogueUrl` dans `config.lua` de chaque script MJ-IA intégrant
Courtier (actuellement `the_diamonds_never_die`). URL brute :
`https://raw.githubusercontent.com/MJ-IAscript/MJ-IAcatalogue/main/catalogue.json`
