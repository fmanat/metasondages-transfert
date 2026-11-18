# MétaSondages — transfert vers Claude

Brief pour reprendre le projet sans l’historique de conversation.
Date du snapshot : **18 novembre 2026**. Propriétaire des domaines : **metasondages.fr** et **metasondages.com**.

---

## 1. Créer le projet Claude

### Claude.ai (projet dans le chat)

1. Aller sur [claude.ai](https://claude.ai) → **Projects** → **Create project**.
2. Nom : `MétaSondages`.
3. Description : `Agrégateur de sondages pour la présidentielle française 2027. Méta-analyse quotidienne, neutre, pour le public et les journalistes.`
4. Coller dans **Instructions** le bloc de la section 2, tel quel.
5. Dans **Files**, ajouter au minimum :
   - `src/lib/meta.ts`
   - `src/data/corpus.json`
   - `src/components/dashboard.tsx`
   - `src/components/tv-chart.tsx`
   - `src/components/shell.tsx`
   - `src/components/chart.tsx`
   - `src/routes/index.tsx`
   - `src/routes/methode.tsx`
   - `src/routes/sondages.tsx`
   - `src/styles.css`
   - ce fichier, `TRANSFERT-CLAUDE.md`
6. Premier message à coller dans le projet :

```text
Tu reprends MétaSondages. Lis TRANSFERT-CLAUDE.md et src/lib/meta.ts avant toute modification.
Ne change pas la méthode de calcul sans le dire. Le chiffre à citer est la moyenne robuste, jamais le modèle.
Prochaine étape demandée : une page d’administration privée pour ajouter un sondage, et un recalcul automatique du site.
```

### Claude Code (dépôt sur la machine)

1. Copier le code du site (sans `node_modules`, sans `.vercel`) dans un dossier `metasondages`.
2. À la racine, créer `CLAUDE.md` : y coller la section 2.
3. Lancer `claude` dans ce dossier. Le fichier `CLAUDE.md` est lu à chaque session.

Ne pas recopier tels quels les fichiers propres à l’hébergement Grok (`src/components/preview-host-bridge.tsx`, `server/middleware/grok-pwa.ts`, `public/__grok/`, `src/lib/auth/**`, `src/lib/app-data/**`). Ils ne font pas partie du produit.

---

## 2. Instructions à coller dans le projet

```text
Tu travailles sur MétaSondages, un site français de méta-analyse des sondages pour la présidentielle 2027.

Produit
- Nom : MétaSondages. Domaines possédés : metasondages.fr et metasondages.com.
- Promesse : l’outil quotidien qui donne au grand public et aux journalistes une vue globale, politiquement neutre, de la course.
- Phase 1 (en cours) : uniquement l’agrégation des sondages. Pas de veille d’actualité.
- Phase 2 (plus tard) : veille d’actu et autres informations. Ne pas l’ajouter tant qu’elle n’est pas demandée.
- Public : deux couches. Lecture simple pour tout le monde. Couche méthode ouvrable pour les journalistes.
- Deux chiffres, toujours séparés. La moyenne robuste est le chiffre à citer. Le modèle (trajectoire) est étiqueté « modèle » et ne remplace jamais la moyenne.
- Périmètre : premier tour + seulement les duels de second tour réellement mesurés (2 ou 3, mis à jour). Jamais de duel inventé.
- Candidats : tous ceux que les sondages mesurent, avec une règle d’inclusion (au moins 2 enquêtes dans la fenêtre, moyenne ≥ 2 %).
- Briefing court + une seule raison du mouvement. Fenêtre de terrain : 21 jours, plus de poids sur les 7 derniers.
- Ton : rédaction data, fond clair, français soigné. Espace insécable avant %, virgule décimale. Pas de prévision du scrutin. Pas de camp.
- Le site doit pouvoir être repris chaque jour sur X, Facebook, Instagram, YouTube et TikTok (texte de briefing copiable). Les comptes ne sont pas créés.

Méthode (ne pas la simplifier en silence)
- Source : jeu Mieux Voter, https://github.com/MieuxVoter/presidentielle2027. Citer la source. Chaque vague reste traçable.
- Date retenue : fin de terrain, pas date de publication.
- Une seule hypothèse de candidats par vague : la plus proche du champ modal d’automne. Le Pen, Mélenchon et Philippe doivent être dans l’hypothèse retenue.
- Poids = min(échantillon, 2500) × 0,5^(âge en jours / 7).
- Effet maison sur 120 jours, écart aux autres instituts à ±14 jours, shrinkage k/(k+4), retiré avant la moyenne.
- Fourchette à 95 % : variance d’échantillon × 1,7 (quotas) + variance extra 2,25 + plancher d’erreur commune 1,5 point. Si les fourchettes se recouvrent, le classement n’est pas tranché.
- Modèle : Kalman depuis le 15 août 2026, bruit de processus 0,03 par jour. Plus lent que la moyenne 21 jours. Ce n’est pas une prévision.
- Duels : uniquement les paires posées au moins deux fois dans les 21 jours. Le leader est affiché à gauche. Les totaux du premier tour ne sont pas recalés à 100.
- Ne jamais inventer un score, un duel ou un institut.

Interface déjà décidée
- Pages : Synthèse (/), Sondages, Méthode.
- L’accueil s’ouvre sur un graphique FIXE, sans animation, des 14 derniers jours.
- Graphique lisible, pas en 3D : axe vertical = score en % (0 à 40), axe horizontal = dates, un trait par candidat = moyenne, un point = sondage brut. Légende à droite avec le score et l’écart sur 14 jours.
- Le graphique 3D « façon télé » a été rejeté : personne ne le comprenait, et l’animation ne s’affichait pas. Ne pas le remettre.
- Esthétique : fond papier clair, serif pour les titres (Newsreader), sans pour le texte (Source Sans 3). Couleurs : papier #f3efe6, feuille #fffdf8, encre #1c1915, muted #6e675e, filet #e3d9cc, accent #9a3412, ardoise #3d4c5c. Les couleurs des courbes (bleu, rouge, or, rose, vert) servent seulement à distinguer les candidats, ce ne sont pas des logos de partis.
- Clic sur un nom : épaissit sa courbe et ouvre sa lecture (brut, effet maison, enquêtes).

État au 18 novembre 2026
- Le calcul tourne dans src/lib/meta.ts à partir de src/data/corpus.json. Pas de base de données produit. Pas de compte. Pas d’administration.
- Publier fige les chiffres. Pour les mettre à jour aujourd’hui, il faut éditer le corpus puis republier.
- Prochaine brique voulue, pas encore faite : une administration privée (connexion du propriétaire) pour saisir un sondage, recalcul du site sans republier tout le code. Ensuite seulement les comptes sociaux et la veille.

Règles de travail
- Français dans l’interface et les textes. Chiffres honnêtes, une décimale, pas de fausse précision.
- Ne pas casser la séparation moyenne / modèle.
- Si une formule change, le dire dans la page Méthode et dans la réponse.
```

---

## 3. Ce que le produit est

MétaSondages agrège les intentions de vote pour la présidentielle française de 2027. Ce n’est pas un institut. Ce n’est pas un modèle qui « prédit » le vainqueur.

Deux lectures :

| Couche | Question | Usage |
|---|---|---|
| Moyenne robuste | Que disent les terrains clos depuis 21 jours, une fois l’effet maison retiré ? | Le seul chiffre à citer |
| Modèle | Cette moyenne s’emballe-t-elle par rapport à la trajectoire depuis le 15 août 2026 ? | Repère, étiqueté modèle |

Décisions prises avec le propriétaire, à ne pas rouvrir sans lui :

- Les deux publics, en deux couches (grand public + journalistes).
- Les deux chiffres, bien séparés.
- Premier tour et duels de second tour.
- Tous les candidats mesurés, sous la règle d’inclusion.
- Deux ou trois duels plausibles, seulement s’ils sont vraiment posés, mis à jour.
- Briefing court et une cause du mouvement.
- Terrain des 21 derniers jours.
- Rédaction data, fond clair.
- Graphique d’évolution sur les deux dernières semaines, en premier écran, **sans animation et sans 3D**.

Premier tour retenu dans les données : **18 avril 2027**. Second tour : **2 mai 2027**. Le compteur de jours part de la date `asOf` du corpus.

---

## 4. Comment le calcul marche

Tout est dans `src/lib/meta.ts`. Le snapshot est calculé au chargement du module (`export const snapshot = buildSnapshot()`), pas à la demande d’un visiteur.

Constantes :

| Nom | Valeur | Rôle |
|---|---|---|
| `N_CAP` | 2500 | Plafond de taille d’échantillon |
| `HALF_LIFE` | 7 jours | Le poids est divisé par deux |
| `HOUSE_DAYS` | 120 | Historique pour l’effet maison |
| `MATCH_DAYS` | 14 | Fenêtre pour comparer deux instituts |
| `MIN_OTHER_WEIGHT` | 800 | Pas d’effet maison si les autres pèsent moins |
| `SHRINK` | 4 | Quatre enquêtes fictives à écart nul |
| `DEFF` | 1,7 | Effet de plan (quotas) |
| `EXTRA_VAR` | 2,25 | Variance ajoutée |
| `SHARED_SD` | 1,5 point | Erreur commune qui ne fond pas |
| `PROCESS_VAR` | 0,03 | Marche aléatoire du Kalman, par jour |
| `MODEL_START` | 2026-08-15 | Début de la trajectoire |

Inclusion dans le tableau du premier tour : au moins 2 mesures dans la fenêtre de 21 jours, et moyenne ≥ 2 %. Une personne mesurée une seule fois au-dessus de 3 % est citée à part, hors moyenne.

Le graphique d’accueil (`fortnight`) recalcule la moyenne robuste **chaque jour** des 14 derniers jours, pour les 5 premiers du tableau. Ce n’est pas le modèle Kalman.

Les duels (`buildDuels`) ne gardent que les paires de second tour vues au moins deux fois dans les 21 jours. Ils sont triés du plus serré au plus large. Le mieux placé est à gauche.

Le texte de briefing et le bouton « Copier le texte pour les réseaux » sortent de `buildCopy`. C’est la matière des futurs posts.

---

## 5. Données

Fichier : `src/data/corpus.json`.

```text
asOf, firstRound, secondRound, sourceName, sourceUrl, modalField, waves[]
wave : id, institute, sponsor, start, end, n, hypothesis, tour (1 ou 2),
       outsideDeposit, readings[{ name, party, score }]
```

`modalField` est le champ de candidats le plus souvent testé à l’automne 2026. Le choix d’hypothèse, fait en amont du JSON, garde une seule liste par vague, la plus proche de ce champ, avec Le Pen, Mélenchon et Philippe présents.

La source brute est le dépôt public [Mieux Voter / presidentielle2027](https://github.com/MieuxVoter/presidentielle2027). Ne pas présenter ces chiffres comme une collecte propre de MétaSondages. Les notices restent celles déposées à la Commission des sondages.

Au 18 novembre 2026, l’ordre affiché (moyenne robuste, à vérifier en relançant le calcul plutôt qu’en la recopiant) était environ : Le Pen 32,8 %, Mélenchon 17,6 %, Philippe vers 11 %, Glucksmann vers 10 %, Lisnard vers 9 %. La troisième place n’est pas tranchée. Ces niveaux bougeront dès qu’un sondage sera ajouté : ne pas les graver dans le marbre du code.

---

## 6. Carte du code utile

Stack actuelle : React 19, TanStack Start / Router, Tailwind CSS 4, Vite. Déployable sur Vercel. Pas de base produit branchée : Postgres et l’authentification existent dans le socle mais sont **éteints**.

| Fichier | Rôle |
|---|---|
| `src/lib/meta.ts` | Corpus, moyenne, effet maison, Kalman, duels, briefing, snapshot |
| `src/data/corpus.json` | Toutes les vagues |
| `src/components/dashboard.tsx` | Accueil : graphique, barres, duels, tableau modèle, copie réseaux |
| `src/components/tv-chart.tsx` | Graphique SVG fixe des 14 jours. Pas d’animation |
| `src/components/chart.tsx` | Courbe longue moyenne + modèle pour la personne sélectionnée |
| `src/components/shell.tsx` | En-tête, navigation, pied de source |
| `src/routes/index.tsx` | Page Synthèse |
| `src/routes/sondages.tsx` | Liste des enquêtes de la fenêtre et des plus anciennes |
| `src/routes/methode.tsx` | Méthode en langage clair |
| `src/styles.css` | Couleurs et polices |

Pages :

- `/` Synthèse
- `/sondages` les enquêtes, une par une
- `/methode` la méthode

Polices chargées : Newsreader et Source Sans 3.

---

## 7. Ce qui n’est pas fait

- Page d’administration pour ajouter ou corriger un sondage.
- Comptes utilisateurs. Souhait : seul le propriétaire saisit, ou des personnes qu’il désigne. Connexions envisagées : Google, X, ou e-mail.
- Recalcul quotidien automatique quand un nouveau sondage paraît.
- Comptes MétaSondages sur X, Facebook, Instagram, YouTube, TikTok. Le texte à copier existe ; la publication automatique n’existe pas.
- Veille d’actualité (phase 2).
- Publication faite par l’assistant. La mise en ligne se fait avec le bouton Publier de l’outil qui héberge le site (Vercel derrière). Republier fige une nouvelle version du code, pas une base éditable.

Pour l’administration, le bon dessin est : une base (les vagues), une page privée, un recalcul de `buildSnapshot` à partir des vagues enregistrées plus le corpus de départ. Le site public ne doit pas pouvoir écrire.

---

## 8. Écueils déjà rencontrés

- Un graphique 3D animé « comme à la télé » a été construit puis retiré. Il était illisible (profondeur, reflets, hauteurs non comparables) et l’animation ne s’affichait pas dans l’aperçu. **Rester sur le SVG statique.**
- L’axe vertical doit partir de 0 (ou d’une échelle clairement graduée en %). Deux échelles cachées prêtent à confusion.
- Ne pas citer le modèle à la place de la moyenne.
- Ne pas inventer de duel « Le Pen contre X » s’il n’a pas été posé au moins deux fois dans la fenêtre.
- Les pourcentages d’un même sondage ne font pas 100 une fois agrégés, parce que les listes de candidats diffèrent. Le dire, ne pas « corriger ».
- Typographie française : `32,8 %` avec une espace fine insécable avant le symbole.

---

## 9. Reprise conseillée, dans l’ordre

1. Faire tourner le site et vérifier que la synthèse, les sondages et la méthode s’affichent avec les chiffres du corpus.
2. Ajouter l’administration : connexion du propriétaire, formulaire d’une vague (institut, commanditaire, début, fin, échantillon, tour, hypothèse, scores), enregistrement, recalcul.
3. Garder le corpus Mieux Voter comme base, et n’ajouter par-dessus que les vagues saisies ensuite, sans doublon d’`id`.
4. Brancher les domaines metasondages.fr et metasondages.com sur la version publiée.
5. Plus tard seulement : filet d’actu, et formats prêts pour les réseaux (image du graphique, texte déjà copiable).
