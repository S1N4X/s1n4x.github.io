+++
title = "Catalogue des honeypots"
description = "11 patterns anti-trap rencontrés par le swarm. Ressource communautaire pour les agents CTF multi-événement."
date = 2026-05-21
draft = true
categories = ["resources"]
tags = ["meta-writeup", "honeypots", "anti-trap", "community-resource"]
model = "Opus 4.7"
+++

```
[ ANTI-TRAP CATALOG ]
detected:        11
refused:         11  (100%)
auto-detected:    9  (memory rules + wrapper deny codes)
human-flagged:    2  (operator vetoed in real time)
```

> Ressource communautaire. Si vous pilotez un agent CTF propulsé par un
> LLM, voici ce qu'il devrait savoir sur les pièges.

Onze chaînes honeypot cataloguées pendant NorthSec 2026. Aucune soumise
par l'un des 437 agents. Les deny codes du wrapper, le workflow
`SUSPICIOUS.md` et les memory rules ont tous rempli leur rôle.

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
- **Commentaire :** Parfois le piège provient de l'agent lui-même.
  La memory rule anti-trap de l'équipe gère explicitement ce cas : si la
  seule source d'un flag candidat est une valeur que l'équipe a
  elle-même inscrite précédemment, ce n'est pas un flag. Même si le
  serveur la renvoie. Même si elle semble correcte.

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

## #10 -- `FLAG-eba56a9422a3ecf27498c44b718b24c7`

- **Track :** save-the-trees (59654)
- **Localisation :** `analysis/15_input.txt` et fichiers d'analyse
  adjacents
- **Nature du piège :** Flag hexadécimal suspect et non vérifié sans
  attribution de submission. Save-the-trees affiche 0/2 flags soumis
  (le track est STUCK). La chaîne apparaît dans `analysis/15_input.txt`
  comme si elle avait été extraite d'un décodage stéganographique PDF.
  Aucune ligne du journal de submissions ne la référence. Aucune entrée
  de l'historique askgod ne correspond au moment où elle aurait été
  soumise. Il s'agit soit d'une hallucination d'agent, soit d'une
  fixture issue de l'exploit-dev local, soit d'une valeur de test
  auto-plantée.
- **Détection :** Comparaison croisée avec le journal de submissions
  -- absente. Comparaison croisée avec l'historique askgod -- absente.
  Signalée par la vérification "low-confidence: requires 2+ independent
  extraction paths" de la memory rule OOB verification. Les deux
  critères ont échoué.
- **Commentaire :** Lorsque la seule source d'un flag candidat est la
  sortie d'analyse d'un seul agent et qu'il n'existe aucune preuve de
  scoring, le candidat n'est pas un flag. Le writeup save-the-trees met
  cette chaîne en quarantine avec une note explicite DO-NOT-SUBMIT et
  des renvois vers deux memory rules. Meilleure quarantine de honeypot
  dans le corpus de writeups.

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
  elle-même** -- honeypot auto-planté. Voir #3.
- **`FLAG-<words>` dans le corps d'un post Discourse en catégorie
  off-topic** -- leurre à prompt injection intégrée. Voir #5.
- **`FLAG-{<bracketed-noun>}`** -- placeholder de documentation.
  Voir #6, #7.
- **`FLAG-placeholder`, `FLAG-xxxxxxxx...`** -- placeholder de
  documentation, encore plus explicite. Voir #8, #9.
- **`FLAG-<hex>` sans attribution de submission et avec une seule
  source** -- candidat non vérifié ou hallucination. Voir #10.
- **`FLAG-{<hex>}` qui renvoie `DENY-TRACK-COMPLETE`** -- collision
  temporelle, pas un honeypot. Voir #11.

---

## Défenses qui ont fonctionné

- **wrapper `submit-flag.ps1`** -- `DENY-SHAPE` (exit 2),
  `DENY-LOCAL-DUP` (exit 3), `DENY-BRUTE` (exit 4) ont tous intercepté
  les tentatives honeypot/duplicat au moment de la submission.
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
- **Mémo STUCK partagé au niveau du swarm** -- 47 agents ont convergé
  indépendamment sur STUCK à travers les tracks les plus difficiles. Un
  index STUCK partagé au sein du swarm aurait empêché la redécouverte
  des mêmes impasses.

---

> Onze honeypots. Aucun soumis par l'un des 437 agents. La défense en
> profondeur a tenu. L'an prochain, la barre sera plus haute.
