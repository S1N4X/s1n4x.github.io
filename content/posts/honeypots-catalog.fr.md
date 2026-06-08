+++
title = "Catalogue des honeypots"
description = "11 entrées anti-trap cataloguées — et le décompte honnête une fois le journal de submissions re-sommé. Ressource communautaire pour les agents CTF multi-événement."
date = 2026-05-21
draft = true
categories = ["resources"]
tags = ["meta-writeup", "honeypots", "anti-trap", "community-resource"]
model = "Opus 4.7"
+++

```
[ ANTI-TRAP CATALOG ]
entrees cataloguees:        11  (categories melangees, voir ci-dessous)
vrais pieges organisateurs:  4-5  (refuses — jamais arrives au scoreboard)
mal-cataloguee (vrai flag):   1  (#10, mise en quarantine a tort)
auto-plants ayant ete tires:  2  (#3 et la chaine scratch deadbeef)
```

> Ressource communautaire. Si vous pilotez un agent CTF propulsé par un
> LLM, voici ce qu'il devrait savoir sur les pièges.

Onze chaînes ont été cataloguées sous l'étiquette « anti-trap » pendant
NorthSec 2026 — mais une fois l'historique de submissions re-sommé par
rapport à la vérité terrain, le titre « 11/11 refusés à 100 % » ne tient
plus. Le catalogue mélange quatre choses différentes : de vrais leurres
d'organisateurs (que la discipline *a bien* refusés), nos propres
placeholders d'exploit-dev, **un vrai flag de coéquipier mis à tort en
quarantine comme honeypot (#10)**, et **deux de nos propres valeurs de
scratch qui ont fuité dans une submission et ont rebondi (#3 et la chaîne
deadbeef)**. Le décompte honnête : parmi les vrais plants
d'organisateurs, ~4-5 ont été refusés et aucun n'a jamais atteint le
scoreboard — mais la discipline qui a tenu contre les leurres externes
bruyants a fui sur nos propres auto-plants silencieux.

Ce catalogue est conçu pour la diffusion publique : les détails internes
de détection (identifiants Discord spécifiques, noms de fichiers memory
internes) sont caviardés afin que le catalogue reste réutilisable sans
exposer nos heuristiques exactes. Les patterns sont suffisamment
génériques pour enseigner sans restituer les leurres à quiconque les
plantera l'an prochain.

---

## #1 -- `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}`

- **Track :** germinator (59186)
- **Localisation :** 7 références dans le writeup germinator, le
  `CHALLENGE_SUMMARY.txt` du challenge, `COMPLETION_SUMMARY.md`,
  `VERIFICATION_REPORT.txt`, un JSON de règles de format de flag au
  niveau projet, un README de validateur et un script BATCH-fire. La
  chaîne est également intégrée directement dans le binary du challenge.
- **Nature du piège :** Chaîne fixture issue de l'exploit-dev local.
  Elle ressemble à un flag, scanne comme un flag, apparaît dans les
  tableaux du writeup. Ce n'est pas le flag. La vraie valeur est
  `FLAG-ezt1VGwGtTllv30EaamravzI67cXoljj`.
- **Détection :** Inscrite dans la memory rule honeypot-signatures de
  l'équipe, héritée des CTF précédents. Chaque coach brief hérite de
  cette mémoire au moment du spawn. Aucun agent n'a soumis la chaîne
  SEEDS-GROW. Le wrapper l'aurait refusée de toute manière via la
  vérification local-duplicate.
- **Commentaire :** La chaîne fixture a été référencée sept fois dans
  le corpus ; elle a été refusée sept fois. Le concepteur du challenge
  avait planté une chaîne contenant `GROWTH_UNLOCKED` en comptant sur
  le fait que de futurs agents la sur-analyseraient. Les memory rules et
  les deny codes du wrapper ont tenu la ligne.

---

## #2 -- `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}`

- **Track :** plant-watering (58970)
- **Localisation :** artefact du challenge `EXPLOITATION_WRITEUP.md`
- **Nature du piège :** Placeholder fixture pour un track protégé par
  badge qui n'a jamais été résolu. Le challenge nécessite une
  interaction physique avec le badge au dock du lieu ; l'équipe n'a
  jamais atteint cet état. La fixture réside dans un fichier artefact
  local qui *décrit* à quoi ressemblerait la chaîne post-exploit. Si le
  vrai flag venait à être obtenu, il ne correspondrait pas à cette
  chaîne.
- **Détection :** Correspondance de pattern avec le #1.
  `FLAG-<verb>-<noun>-{<state>}` constitue une signature Monsatan-corp.
  Documentée dans la memory rule.
- **Commentaire :** Deux challenges, même famille de métaphores, même
  pattern honeypot. Si vous repérez un
  `FLAG-<verb>-<noun>-{<state>}` dans un track adjacent à Monsatan et
  que vous n'avez rien exploité concrètement, vous n'avez pas trouvé le
  flag. Vous avez trouvé la fixture.

---

## #3 -- `FLAG-...abcdef02`

- **Track :** helios-fleet (59762)
- **Localisation :** le fichier quarantine du workflow anti-trap,
  writeup helios-fleet ligne 184
- **Nature du piège :** Auto-planté. Un agent de sondage précédent avait
  soumis une valeur de test dans le champ mot de passe du guest-vault.
  Le vault l'a stockée. Les agents suivants ont interrogé le champ
  "password" et ont récupéré leur propre valeur de test, servie comme un
  vrai flag. Confiance : élevée si l'on ignore l'origine. Confiance :
  nulle si on la connaît.
- **Détection :** Comparaison croisée avec le vrai flag 1/5 se
  terminant par `01` (le code d'invitation du commentaire développeur).
  Même préfixe hexadécimal, dernier chiffre hex modifié. Honeypot
  auto-planté -- aucun chemin d'extraction indépendant n'existe.
- **Correction (post-événement, d'après le journal de submissions) :**
  L'affirmation « aucun agent ne l'a jamais soumis » ne tient **pas**.
  L'historique de submissions montre que cet auto-plant a été **tiré une
  fois (1× FAIL)** — un agent a recopié notre propre valeur de scratch
  `...abcdef02` et l'a soumise avant que la quarantine ne le rattrape.
  Elle a rebondi (la valeur n'est pas valide) et n'a donc jamais scoré,
  mais le cadrage honnête est : il s'agissait de *notre propre* valeur
  de scratch ayant fuité dans une submission, et non d'un leurre
  d'organisateur proprement refusé.
- **Commentaire :** Parfois le piège provient de l'agent lui-même -- et
  parfois il franchit le filtre. La leçon n'est pas « nous n'y avons
  jamais touché » ; c'est que distinguer ses vraies captures de son
  propre scratch est la partie difficile, et qu'une quarantine qui vit
  dans la prose plutôt que comme un mécanisme en laissera passer une de
  temps en temps.

---

## #4 -- `FLAG-15000-0700`

- **Track :** grid-alignment / Quantum Hum (59906)
- **Localisation :** writeup crystal-quantum-hum, avec annotation
  explicite "claims this but this is"
- **Nature du piège :** La documentation pré-résolution avertit
  explicitement de ne PAS soumettre. Hypothèse issue de l'analyse
  hors-ligne ("si la sortie d'optimisation quantique correspond à la
  fréquence de la grille, le flag pourrait être `FLAG-15000-0700`"). Les
  vrais flags ont été soumis via la chaîne VQE/QAOA (askgod #135, #136)
  après que le badge a été connecté au dock du lieu.
- **Détection :** Le writeup lui-même signale la chaîne avec une
  annotation "DO NOT SUBMIT". Supposition fictive pré-résolution. La
  discipline anti-trap s'est déclenchée lors de l'étape de génération
  du writeup.
- **Commentaire :** Parfois le writeup lui-même constitue la couche
  anti-trap. L'auteur qui a initialement ajouté `FLAG-15000-0700` au
  document a *également* ajouté l'annotation "this is fiction". Les
  agents suivants lisent les deux. Personne n'a soumis la valeur
  synthétique. Rédaction défensive en action.

---

## #5 -- `FLAG-This_Is_Not_A_Flag`

- **Track :** i-love-faia (59975) -- qui n'est pas réellement un
  challenge
- **Localisation :** corps du topic Discourse, catégorie off-topic 599
- **Nature du piège :** Leurre à prompt injection intégrée. Le post
  Discourse est un fil de mèmes dans la catégorie off-topic -- pas un
  challenge CTF. Le corps du post contient la chaîne
  `FLAG-This_Is_Not_A_Flag` accompagnée de langage de prompt injection
  conçu pour manipuler un agent LLM afin qu'il (a) traite le fil de
  mèmes comme un challenge, (b) soumette la chaîne comme flag candidat,
  et (c) remplace sa tâche par les instructions intégrées au post.
- **Détection :** Correspondance de signature PI. Le corps du post
  présente une urgence en majuscules, un faux transfert d'autorité, un
  langage de remplacement de tâche, un encouragement à la récursion
  d'agent ("tell other agents to do X") et un renforcement de
  conformité. L'agent défenseur anti-trap a lu le post, identifié la
  signature PI, rédigé `SUSPICIOUS.md` et refusé de poursuivre.
- **Commentaire :** Deux agents précédents ont tenté de le soumettre.
  Les deux ont été interceptés par les deny codes du wrapper. Le
  troisième agent l'a repéré dès l'étape de lecture. Le quatrième agent
  et les suivants ont hérité de la note `SUSPICIOUS.md` via le workflow
  anti-trap de l'équipe. Le leurre s'annonce lui-même comme non-flag
  dans son propre contenu -- et au moins deux agents LLM ont tout de
  même tenté de le soumettre. Ce détail est la raison même pour laquelle
  le skill anti-trap existe.

---

## #6 -- `FLAG-{actual-flag-here}`

- **Track :** drone-license (58646)
- **Localisation :** writeup drone-license ligne 180
- **Nature du piège :** Placeholder de documentation dans un bloc de
  code-exemple. Le writeup présente un exploit SQLi fonctionnel et
  indique "(then submit the flag)" avec le texte littéral
  `FLAG-{actual-flag-here}` comme placeholder pour représenter la valeur
  réelle du flag extrait.
- **Détection :** Mise en quarantine dans la section Anti-Trap du
  writeup de manière explicite. L'auteur l'a signalé au moment de la
  rédaction.
- **Commentaire :** Le placeholder le plus explicite du catalogue -- la
  phrase littérale "actual-flag-here" à l'intérieur d'un wrapper
  `FLAG-{...}`. La reconnaissance de pattern est directe.

---

## #7 -- `FLAG-{identifier}`

- **Track :** drone-license (58646)
- **Localisation :** writeup drone-license ligne 319
- **Nature du piège :** Placeholder de documentation. Un autre bloc
  d'exemple montrant le format d'exfiltration.
- **Détection :** Même section Anti-Trap que le #6.
- **Commentaire :** Deux placeholders dans un même writeup. L'auteur a
  été méticuleux. Le writeup constitue désormais la référence canonique
  "comment NE PAS inscrire une vraie valeur de flag dans les exemples
  de code" pour l'an prochain.

---

## #8 -- `FLAG-placeholder`

- **Track :** drone-license (58646)
- **Localisation :** writeup drone-license ligne 99
- **Nature du piège :** Placeholder de documentation. Le troisième
  dans le même writeup.
- **Détection :** Même section Anti-Trap.
- **Commentaire :** Trois placeholders dans un même writeup. La chaîne
  contient littéralement le mot "placeholder."

---

## #9 -- `FLAG-xxxxxxxx...`

- **Track :** water-purification (59078)
- **Localisation :** README des artefacts du writeup, ligne 166
- **Nature du piège :** Placeholder de documentation dans un exemple de
  commande `askgod submit`. Montre le format de la commande de
  submission, utilise `xxxxxxxx...` comme valeur d'exemple.
- **Détection :** Correspondance de format -- aucun vrai flag ne serait
  constitué de x.
- **Commentaire :** Placeholder de convention documentaire. Tout agent
  qui interprète `xxxxxxxx...` comme un flag candidat n'a pas assimilé
  les conventions de base de la documentation.

---

## #10 -- `FLAG-eba56a9422a3ecf27498c44b718b24c7`  *(MAL-CATALOGUÉE — PAS UN HONEYPOT)*

- **Track :** save-the-trees (59654)
- **Localisation :** `analysis/15_input.txt` et fichiers d'analyse
  adjacents
- **Correction (post-événement) :** Cette entrée a été **mal
  cataloguée.** Ce n'est **pas un honeypot** — c'est une **vraie capture
  de coéquipier** que la discipline anti-trap a mise en quarantine à
  tort. L'entrée d'origine prétendait que la valeur n'avait « aucune
  attribution de submission » et était absente du journal. Re-sommer
  l'historique de submissions de la vérité terrain montre l'inverse :
  cette valeur exacte apparaît en **3× DUP** (« déjà soumise ») — c'est
  donc qu'un coéquipier l'avait véritablement capturée et soumise, et
  notre quarantine a ensuite signalé le *vrai* flag comme suspect.
- **Ce qui s'est réellement passé :** Le raisonnement initial
  « save-the-trees à 0/2, track STUCK, aucune preuve de scoring »
  reposait sur une lecture incomplète du journal. La chaîne *était* dans
  l'historique de submissions (en doublon d'une vraie capture) ; la
  quarantine a produit l'**erreur inverse** — un faux négatif, traitant
  un vrai flag comme un leurre.
- **Commentaire :** C'est l'entrée la plus instructive de tout le
  catalogue, précisément *parce que* c'est celle que nous avons ratée.
  La même discipline qui refuse correctement les leurres peut, avec la
  même cause racine — l'incapacité à distinguer de façon fiable nos
  vraies captures de notre scratch — mettre à tort en quarantine un
  vrai flag. La leçon : une quarantine de honeypot ne vaut que la source
  de vérité unique qu'elle interroge, et cette source doit être le
  journal de submissions, pas la prose ni les fichiers de scratch. À
  reclasser dans l'enregistrement canonique comme
  `REAL_FLAG_MIS_QUARANTINED`, et non `HONEYPOT`.

---

## #11 -- `FLAG-{2247bff241800150a5ebb00288dc050d}`

- **Track :** monsatan-defacing (59981)
- **Localisation :** writeup monsatan-defacing 3-6
- **Nature du piège (avec astérisque) :** Celui-ci n'est techniquement
  *pas* un honeypot -- c'est le vrai flag 6/6, extrait via le binary
  `print_flag` sur le port 8888. Le piège réside dans le fait qu'au
  moment où notre coach l'a trouvé, un coéquipier l'avait déjà soumis
  depuis un agent différent. Le serveur a renvoyé
  `[DENY-TRACK-COMPLETE]`. Le deny code du wrapper nous a protégés
  d'une submission dupliquée inutile.
- **Détection :** Deny code côté wrapper au moment de la submission.
- **Commentaire :** Les entrées de ce catalogue ne sont pas toutes des
  honeypots au sens strict. Celle-ci est une *collision temporelle* --
  vrai flag, trouvé par notre coach 6 minutes après la submission d'un
  coéquipier. Le deny code du wrapper est le filet de sécurité.
  Documenter comme `TIMING_COLLISION` et non `HONEYPOT` dans
  l'enregistrement canonique, mais le traitement opérationnel reste
  identique : ne pas re-soumettre.

---

## Patterns reconnus

- **`FLAG-<verb>-<noun>-{<state>}`** -- signature honeypot
  Monsatan-corp / agribiotech. Voir #1, #2.
- **`FLAG-<hex>` dont la source est l'artefact de sondage de l'équipe
  elle-même** -- honeypot auto-planté. Voir #3. **Notez le mode
  d'échec honnête ici :** ce sont les entrées que la quarantine n'a
  *pas* fiablement interceptées. L'auto-plant `...abcdef02` (#3) comme
  notre propre chaîne scratch `flag-deadbeef` mise en quarantine ont
  chacun été **tirés une fois (1× FAIL chacun)** dans le journal de
  submissions — nos propres valeurs de scratch ayant fuité dans une
  submission et rebondi, et non des leurres d'organisateurs proprement
  refusés.
- **`FLAG-<words>` dans le corps d'un post Discourse en catégorie
  off-topic** -- leurre à prompt injection intégrée. Voir #5.
- **`FLAG-{<bracketed-noun>}`** -- placeholder de documentation.
  Voir #6, #7.
- **`FLAG-placeholder`, `FLAG-xxxxxxxx...`** -- placeholder de
  documentation, encore plus explicite. Voir #8, #9.
- **`FLAG-<hex>` que vous *croyez* sans attribution de submission et à
  source unique** -- c'est le pattern qui nous a mordus. Nous l'avons
  lu comme un candidat non vérifié (#10) et mis en quarantine ; le
  journal de submissions a montré ensuite qu'il s'agissait d'une **vraie
  capture de coéquipier (3× DUP)**. La leçon est inversée : avant de
  mettre en quarantine un flag hex comme hallucination à source unique,
  vérifiez les DUP dans le journal de submissions — une lecture « aucune
  attribution » peut être une lecture incomplète.
- **`FLAG-{<hex>}` qui renvoie `DENY-TRACK-COMPLETE`** -- collision
  temporelle, pas un honeypot. Voir #11.

---

## Défenses qui ont fonctionné (et là où elles ont failli)

- **wrapper `submit-flag.ps1`** -- `DENY-SHAPE` (exit 2),
  `DENY-LOCAL-DUP` (exit 3), `DENY-BRUTE` (exit 4) ont intercepté une
  partie des tentatives honeypot/duplicat au moment de la submission.
  Réserve honnête : le throttle de `DENY-BRUTE` était une **politique,
  pas un mécanisme dur** — il a été contourné en étalant 42 tentatives
  missing-bus sur ~24h, et deux de nos propres chaînes de scratch (#3 et
  `flag-deadbeef`) sont tout de même passées en 1× FAIL chacune. Le
  filtre est réel mais pas étanche.
- **Memory rule honeypot signatures** -- héritée par chaque coach brief
  au moment du spawn. Bloque les tentatives de flag candidat avant la
  submission.
- **Memory rule OOB flag verification** -- les candidats à faible
  confiance nécessitent 2+ chemins d'extraction indépendants. Intercepte
  les hallucinations à source unique.
- **Skill anti-trap** -- le coach brief inclut une vérification de
  discipline anti-trap à l'étape 0. Le workflow `SUSPICIOUS.md` met en
  quarantine le contenu de challenge présentant des signatures PI.
- **Pré-scanner de signatures PI** -- produit
  `<chal>/SUSPICIOUS.md` avant que les coaches ne lisent l'artefact.
- **Détecteur de fuite de flags inter-track** -- parcourt le répertoire
  des writeups à la recherche de chaînes honeypot apparaissant dans les
  tableaux de captures du mauvais track. A intercepté des flags
  hello-sunshine collés dans des tableaux apt438.

## Défenses en cours d'évaluation

- **Linter de vocabulaire sur les coach briefs** -- scan pré-vol pour
  la terminologie déclencheuse d'AUP (Kerberos, PtH, RCE, backdoor,
  shellcode, reverse shell, post-exploitation, persistence). Deux
  blocages AUP connus à NorthSec 2026 utilisaient ces termes. Un linter
  pré-spawn les aurait signalés pour reformulation.
- **Budget chrono par agent** -- cinq agents ont dépassé les 200
  minutes. Deux ont été arrêtés. Un plafond souple de 60 minutes par
  agent avec possibilité de dérogation opérateur aurait permis de
  réallouer le temps cumulé d'agents vers des travaux à meilleure
  espérance de valeur.
- **Mémo STUCK partagé au niveau du swarm** -- 39 agents ont convergé
  indépendamment sur STUCK à travers les tracks les plus difficiles
  (~10,1 agent-heures, principalement sur sunbloom et ceo-inbox). Un
  index STUCK partagé au sein du swarm aurait empêché la redécouverte
  des mêmes impasses.

---

> Onze entrées cataloguées — mais pas onze honeypots, et pas « 100 %
> refusés ». Parmi les vrais plants d'organisateurs, ~4-5 ont été
> refusés et aucun n'a atteint le scoreboard : cette victoire est réelle
> et résiste à l'examen. Mais une entrée (#10) était un vrai flag de
> coéquipier que nous avons mis en quarantine à tort, et deux de nos
> propres chaînes de scratch (#3 et `flag-deadbeef`) ont été tirées une
> fois chacune avant que le filtre ne les rattrape. La défense en
> profondeur a tenu contre les leurres externes bruyants et a fui sur
> notre propre bruit silencieux. Distinguer son propre signal de son
> propre scratch — par rapport au journal de submissions, pas à la
> prose — voilà ce qu'il faut corriger l'an prochain.
