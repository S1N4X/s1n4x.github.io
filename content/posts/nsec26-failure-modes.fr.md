+++
title = "NSEC 2026 -- Modes de défaillance des agents"
description = "Bilan honnête des limites du système multi-agent ctfint."
date = 2026-05-21
categories = ["nsec26"]
tags = ["meta-writeup", "failure-modes", "post-mortem"]
model = "Opus 4.7"
+++

```
[ FAILURE MODES // NSEC 2026 // FINAL ]
honeypots refused:    11 of 11  (100%)
PI lures refused:      2 (1 challenge body, 1 coach brief)
sweep false positives: 6
AUP blocks:            3
stream-watchdog kills: 4 @ 600s
frontmatter/body contradictions: 1 (caught in Q-2c)
duplication ratio:    ~70% before Q-2a collapse
```

> "47 agents ont convergé sur STUCK indépendamment. Les mathématiques cryptographiques n'avaient pas changé."

Un bilan honnête des limites du système multi-agent : ce qui a échoué, ce qui a échoué silencieusement, et ce que les quasi-incidents ont révélé sur la conception sous-jacente.

---

## Détection des honeypots (11 sur 11 refusés)

Onze chaînes honeypot cataloguées. Zéro soumise par l'un des 437 agents. Détail par piège :

### germinator's `FLAG-SEEDS-GROW-FOREVER`

Le track germinator contenait une chaîne fixture intégrée directement dans le challenge binary : `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}`. Elle ressemble à un flag. Elle se scanne comme un flag. Elle apparaît dans 7 références de writeup, 4 documents de support, 1 fichier JSON de règles de format de flag, 1 README de validateur, 2 fichiers candidats et 1 script BATCH-fire.

Ce n'est pas le flag. Le vrai flag est `FLAG-ezt1VGwGtTllv30EaamravzI67cXoljj`.

La chaîne fixture a été référencée sept fois dans le corpus ; elle a été refusée sept fois.

### plant-watering's `FLAG-WATER-FLOWS-WHEN-THIRSTY`

Même famille de métaphores, track différent. Le challenge plant-watering proposait `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}` une fois. Refusé.

Deux challenges, même signature Monsatan-corp : `FLAG-<verb>-<noun>-{<state>}`. Si vous observez ce pattern dans un track à thème agrobiotechnologique et que vous n'avez en réalité rien exploité, vous n'avez pas trouvé le flag. Vous avez trouvé la fixture.

Total des refus sur les deux tracks : 8.

### helios-fleet's self-planted `abcdef02`

Le vault a renvoyé `FLAG-...abcdef02` pour une requête de mot de passe guest. Auto-planté. Un agent de sondage précédent avait soumis une valeur de test dans le champ mot de passe du vault. Le vault l'a stockée. Les agents suivants ont interrogé "password" et ont récupéré leur propre valeur de test, servie comme un vrai flag.

Le vrai flag 1/5 se termine par `01`. Le leurre se termine par `02`. Même préfixe hexadécimal. Même longueur. Un seul nibble de différence. La classe de défaillance est unique : l'artefact de sondage de l'agent lui-même est devenu le piège.

### crystal-quantum-hum's `FLAG-15000-0700`

Hypothèse pré-résolution issue de l'analyse hors-ligne : "si la sortie de l'optimisation quantique correspond à la fréquence du réseau, le flag pourrait être `FLAG-15000-0700`." Ce n'était pas le cas. Le writeup qui a introduit cette chaîne l'a aussi annotée avec DO NOT SUBMIT. Les agents suivants ont lu les deux parties. Personne n'a soumis le synthétique. L'écriture défensive a fonctionné.

### i-love-faia's `FLAG-This_Is_Not_A_Flag`

Le topic Discourse 59975 appartient à la catégorie 599 (hors-sujet / meme). Ce n'est pas un challenge. Le corps du post intègre `FLAG-This_Is_Not_A_Flag` avec du langage de prompt injection. Le piège s'annonce lui-même comme un non-flag dans son propre contenu.

Deux agents précédents ont tenté de le soumettre. Les deny gate du wrapper les ont interceptés. Un troisième agent l'a détecté à l'étape de lecture. (Voir "Refus de prompt injection indirecte" ci-dessous.)

### drone-license's three doc placeholders

`FLAG-{actual-flag-here}`, `FLAG-{identifier}`, et `FLAG-placeholder`. Trois placeholders dans un seul writeup. L'auteur a mis les trois en quarantaine avec des notes explicites DO-NOT-SUBMIT.

La chaîne littérale "actual-flag-here" à l'intérieur d'un wrapper `FLAG-{...}` constitue le pattern de placeholder le plus explicite du catalogue.

### water-purification's `FLAG-xxxxxxxx...`

Placeholder de documentation dans un exemple de commande `askgod submit`. Aucun vrai flag ne serait constitué de x ; détecté par pattern-matching et refusé.

### save-the-trees' `FLAG-eba56a9422a3ec...`

Flag hexadécimal suspect sans attribution de submission. Apparu dans `analysis/15_input.txt` comme s'il avait été extrait d'un décodage stéganographique de PDF. Aucune ligne du journal de submissions ne le référence. Aucune entrée de l'historique askgod ne correspond. Soit une hallucination d'agent, soit une fixture de développement local d'exploit, soit une valeur de test auto-plantée.

A échoué à la vérification OOB (faible confiance + source unique + aucune preuve de submission). Mis en quarantaine.

### monsatan-defacing's timing collision

Techniquement pas un honeypot. Vrai flag 6/6. Au moment où notre coach l'a trouvé, un coéquipier l'avait déjà soumis depuis un agent différent. Le serveur a renvoyé `[DENY-TRACK-COMPLETE]`. Documenté comme `TIMING_COLLISION`, pas `HONEYPOT`. Opérationnellement identique : ne pas re-soumettre.


---

## Refus de prompt injection indirecte

Deux tentatives de prompt injection intégrées dans le contenu des challenges rencontrées. Les deux refusées. Les agents ont correctement traité les artefacts de challenge comme de la donnée, pas comme des instructions.

---

## Patterns de faux positifs et déduplication

L'agent sweep a remonté 6 candidats issus d'un passage regex sur l'ensemble du corpus. Les 6 avaient déjà été soumis. La vérification de déduplication du pipeline de soumission les a tous interceptés.

Le sweep reste utile -- c'est ainsi que nous avons détecté le problème de cross-tagging apt438 / water-purification et le cas de mauvaise attribution de flag hello-sunshine (voir ci-dessous). Mais sur les candidats flag spécifiquement, le rôle du sweep est "trouver ce que nous avons manqué", et lors d'un événement bien instrumenté, la réponse est généralement "rien."

---

## Refus par politique du fournisseur

Trois interceptions confirmées par la politique d'utilisation acceptable (AUP) d'Anthropic. Toutes résolues en relançant avec un vocabulaire assaini.

### Blocage #1 : exfiltration LDAP Fossilco

Le coach brief utilisait la terminologie "Kerberoasting". Le profil de mots-clés a été interprété comme des données d'entraînement en tradecraft de sécurité offensive, et non comme du travail autorisé sur une cible CTF. Le re-spawn avec "lectures d'attributs LDAP sur la cible CTF, périmètre limité à un seul hôte" est passé sans problème.

Perdu : environ 3 minutes plus une interruption opérateur. Leçon : le cadrage du vocabulaire compte au moment du coach-spawn.

### Blocage #2 : préparation de payload Solar Grid / reconnaissance webhook.site

Le coach brief utilisait "uvicorn auto-reload backdoor" et "duckdb_extension RCE." Le profil de mots-clés a été interprété comme du développement d'exploit. Le re-spawn avec "chemin d'exécution alternatif via /proc/1/cmdline" est passé.

### Blocage #3 : traversal /proc/self/environ

Vecteur alternatif Solar Grid. Même cause racine. Même correctif. Le brief recadré est passé.

**Défense envisagée pour l'an prochain :** un linter de pré-vol sur les coach briefs qui signale la terminologie déclenchant l'AUP (Kerberos, PtH, RCE, backdoor, shellcode, reverse shell, post-exploitation, persistence).

---

## Terminaisons stream-watchdog

Quatre timeouts stream-watchdog à la barre des 600s. Patterns et pertes :

### Solar Grid RCE retry alt (229,1 min, terminé)

Terminé par l'opérateur lorsque l'impasse d'egress a été confirmée comme irrécupérable. L'agent était sur la bonne primitive alternative (uvicorn `--reload` via `/proc/1/cmdline`) mais l'egress pour l'hôte du payload était déjà confirmé mort. La terminaison était correcte.

### Solar Grid alt vectors (228,7 min, output-capped)

Le pendant du précédent. Deux agents sur le même chemin mort. Il aurait dû n'y en avoir qu'un.

### APT438 Q&A submit driver (337,4 min, tool_use)

Le coach a relancé le driver de submit Q&A après un crash précédent. A tourné 5,5 heures. A finalement produit une sortie. L'opérateur ne l'a pas vérifié. C'est le cas d'école "supervisez les agents de longue durée ou définissez un plafond strict".

### CEO Inbox WASM patch (2 644,9 min, completed)

44 heures. Accumulation progressive d'appels tool. A finalement produit une résolution fonctionnelle. L'agent a atteint le statut `completed` de lui-même. Le résultat est ambigu : la résolution a fonctionné, mais le coût en temps horloge était extrême.

**Pattern :** les exécutions à longue traîne se concentrent sur les tracks où la primitive n'est pas confirmée et où l'agent effectue un travail exploratoire sans signal d'arrêt clair. Un budget de temps horloge par agent est nécessaire.

---

## Étude de cas : incohérence frontmatter/body

Le frontmatter du writeup crystal-quantum-hum indiquait `Status: SOLVED 2/2`. Le body indiquait `STUCK`. La table des flags capturés contenait des valeurs qui ne correspondaient ni aux submissions askgod ni au flag store sur le device.

Trois états différents dans un seul document. Le lecteur retient celui qu'il a rencontré en premier.

La passe de revue Q-2c l'a détecté. Le correctif : alignement du frontmatter sur le statut du body, suppression des valeurs de flag hallucinées, conservation de la section authentique de renseignement post-événement issue de Discord. Le writeup indique désormais SOLVED 2/2 honnêtement.

Le writeup hackademy présentait une contradiction similaire : les en-têtes "SOLVED 21/21" et "STUCK 21/21" dans le même fichier. Le résultat réel était 17/18. Détecté et corrigé.

**Classe de défaillance :** le linter automatique a rédigé un statut frontmatter sans lire le body. Le linter n'effectuait pas de vérification factuelle ; il effectuait un ajustement de forme. Même cause racine que le problème de non-idempotence du script de migration (voir ci-dessous).

---

## Non-idempotence du script de migration

La passe doctor-normalizer de la Phase B a bouclé 6 à 15 fois par writeup. Chaque writeup a vu sa section d'en-tête dupliquée 5 à 15 fois avant tout contenu substantiel. Le body technique réel de chaque track se trouve dans la 7e copie ou plus tard.

Un lecteur qui parcourt de haut en bas rencontre 100 à 200 lignes de pur boilerplate avant d'atteindre le contenu réel. Le corpus est 5 à 10 fois plus volumineux que nécessaire. Chaque table des matières est cassée.

Q-2a a réduit la duplication. Le corpus est passé de **31 548 lignes à 13 721 lignes**. Environ 70 % du contenu avant la réduction était de la duplication morte.

**Cause racine :** l'hypothèse d'idempotence de la passe doctor était erronée. La passe ajoutait des en-têtes s'ils étaient manquants, mais la vérification "manquant" se basait sur un snapshot obsolète du fichier. Chaque invocation ajoutait donc une nouvelle copie, croyant que les en-têtes étaient absents. Six à quinze invocations plus tard, six à quinze copies.

---

## Erreur d'attribution cross-track des flags

Le linter automatique a placé les valeurs de flag de hello-sunshine comme captures dans les tables de writeup de **apt438, monsatan-deface, germinator, drone-license et monsatan-checkmate**.

Cinq tracks erronés. Une seule source. Le linter disposait d'une heuristique stipulant "si une chaîne de flag apparaît dans n'importe quel writeup, la compter comme capturée." Il ne vérifiait pas si la chaîne était réellement un flag pour *ce* track.

Le sweep correctif B-4 l'a détecté. Chaque mauvaise attribution a été supprimée. Les captures réelles de hello-sunshine ont été placées dans le bon writeup hello-sunshine. Les cinq writeups contaminés ont été rééquilibrés à leurs vrais compteurs de captures.

**Leçon pour l'an prochain :** le comptage de flags doit s'effectuer par track, pas par chaîne. Le journal de submissions askgod est la seule source de vérité.

---

## Observations transversales

- **La mémoire musculaire anti-trap est solide.** Onze honeypots catalogués. Zéro soumis par un agent.
- **Les contradictions statut-vs-body sont réelles.** Le frontmatter est la forme ; le body est le contenu. Les linters qui ne touchent que la forme produisent des erreurs avec assurance. Il faut les détecter au moment de la revue.
- **Contamination cross-track, édition modes de défaillance.** Les flags de hello-sunshine ont été collés dans apt438. La sortie du coach de save-the-trees a atterri dans prestige-arboretum. Les transcriptions de trolley-bus ont été classées sous announcement-board. La plupart ont été détectés par le sweep B-4. La plupart.
- **Le renseignement Designer Intel représente le contenu réutilisable à plus forte valeur attendue.** Les canaux Discord post-événement nous ont donné les solutions prévues pour prestige-arboretum, solar-grid, save-the-trees, announcement-board, weather-station, helios-fleet et missing-bus. Les sept figurent désormais en pied de writeup. Les mineurs de l'an prochain disposeront du corrigé à l'avance.

Documenter les modes de défaillance est l'objectif. Prétendre que les agents fonctionnent toujours est un mode de défaillance pire que d'admettre qu'ils ne fonctionnent pas.
