# MJ-IAcatalogue

Catalogue vitrine **cross-script** consommé par le module Courtier (NPC vitrine, présent dans
Vangelico et les futurs scripts MJ-IA). Ce repo est **public volontairement** : `catalogue.json`
est récupéré directement depuis le serveur FXServer de chaque acheteur via
`raw.githubusercontent.com` (aucune authentification possible côté script vendu).

Ne contient **aucun code** — uniquement de la donnée marketing (textes, images). Éditer
`catalogue.json` directement ici (interface GitHub ou `git push`) met à jour la vitrine de tous les
acheteurs sous 24h max (`catalogueTtlSeconds` côté script), sans toucher au code ni republier de
version.

**Ce qui se pilote d'ici** : les scripts, les actualités. **Ce qui est dans le script** (changer demande une
nouvelle version) : Discord, Facebook, boutique (noms en dur, toujours affichés) et la mise en page.

## Schéma

```jsonc
{
  "version": 1,
  // Position de la zone Actualités (racine, optionnel) : "bottom" (défaut, sous la liste des scripts)
  // ou "top" (au-dessus). Toute autre valeur vaut "bottom".
  "news_position": "bottom",
  // Actualités (racine, optionnel) — 3 premières entrées affichées, PAS triées par
  // published_at : l'ordre du tableau JSON est l'ordre d'affichage, à organiser à la main.
  // Un clic sur une actualité ouvre une fiche (titre, photo, texte complet).
  "items": [
    {
      "type": "upcoming", // "upcoming" | "update" | "announcement"
      "title": "...",
      "teaser": "...",       // texte court de la carte (upcoming surtout)
      "body": "...",         // texte complet de la fiche (carte : 2 lignes)
      "size": "compact",     // optionnel : "compact" (défaut) | "large" (photo en bandeau dans la liste)
      "image": "https://...", // optionnel : photo de la fiche (https uniquement), cf. « Photos » ci-dessous
      "media_url": "https://...", // upcoming — adresse affichée en TEXTE dans la fiche (ex. bande-annonce)
      "link": "https://...",      // update / announcement — adresse affichée en TEXTE dans la fiche
      "expires_at": "2026-12-31T00:00:00Z" // ISO 8601, optionnel — entrée masquée une fois expirée
    }
  ],
  "scripts": [
    {
      "resourceName": "the_diamonds_never_die", // nom EXACT de la resource FXServer
      "label": "The Diamonds Never Die", // ou { "fr": "...", "en": "...", "es": "..." }
      "description": { "fr": "...", "en": "...", "es": "..." }, // ou chaîne simple
      "image": "html/img/xxx.png",            // optionnel, résolu via nui://<resourceName>/... ou https://...
      "detailBackground": "html/img/xxx.png"  // optionnel, même résolution, SI le script est installé
    }
  ]
}
```

Les anciens champs `discord_link`, `facebook_link`, `tebexUrl` et `tebex_label` ne servent plus : ils sont ignorés
s'ils sont encore présents.

`label`/`description` acceptent une chaîne simple (affichée identique dans toutes les langues) ou
une table `{fr, en, es}` résolue côté client selon `Config.SubLang` — toujours préférer la table.

`image`/`detailBackground` d'un **script** sont résolus par le client du script concerné via
`nui://<resourceName>/<chemin>` — le chemin est relatif à la RACINE de la resource FXServer
installée chez l'acheteur, pas à ce repo — ou sont pris tels quels s'ils commencent par `https://`.
Un script non installé reste affichable (label/description) mais sans fond de fiche.

## Photos des actualités

`image` d'une actualité doit être une adresse **`https://`** publique (les autres schémas — `http:`, `data:`,
`javascript:`, `nui:` — sont refusés, ainsi que les adresses avec espace ou guillemet). Le plus simple : déposer la
photo dans `images/` de ce repo, puis l'adresse est
`https://raw.githubusercontent.com/MJ-IAscript/MJ-IAcatalogue/main/images/<fichier>`. Garder des photos légères
(JPG ou WebP, moins de 200 Ko). Une photo injoignable disparaît sans casser l'actualité.

## Sécurité

Ce repo est public et lu par les joueurs : aucun HTML ni CSS n'est interprété, tous les textes sont affichés comme du
texte, et la mise en page se limite aux valeurs fixes ci-dessus. Ne jamais y mettre de clé, de secret ni de donnée
personnelle (un contrôle automatique refuse les motifs de clé).

## Utilisation

Consommé par `Config.Courtier.catalogueUrl` dans `config.lua` de chaque script MJ-IA intégrant
Courtier (actuellement `the_diamonds_never_die`). URL brute :
`https://raw.githubusercontent.com/MJ-IAscript/MJ-IAcatalogue/main/catalogue.json`

Pour tester sans rien publier : travailler sur la branche `brouillon` et pointer `catalogueUrl` du serveur de dev
sur `.../MJ-IAcatalogue/brouillon/catalogue.json`.
