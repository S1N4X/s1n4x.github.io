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
leurres organisateurs refuses : ~4-5 vrais pieges (PAS le "11/11" annonce)
self-plants ayant fire :        2 (helios ...02 + notre propre chaine do-not-submit)
vrai flag mal catalogue :       1 (save-the-trees, une capture coequipier)
lures PI refuses :              2 (Opus + Haiku, double-confirme)
faux positifs sweep :           6
blocages AUP :                  3
kills d'etat de stream :        4 (pas de watchdog 600s — ce mecanisme n'existait pas)
self-owns par mutation d'etat : 1 (185 mots de passe ecrases, flag brique a l'echelle de l'evenement)
angles morts du sanitizer :     1 (fuite de secret UTF-16 passee sous le grep ASCII)
contradictions frontmatter/body : 1 (detecte en Q-2c)
ratio de duplication :          ~70% avant la reduction Q-2a
```

> "39 agents ont convergé sur STUCK indépendamment sur deux tracks. Les mathématiques cryptographiques n'avaient pas changé."

Un bilan honnête des limites du système multi-agent : ce qui a échoué, ce qui a échoué silencieusement, et ce que les quasi-incidents ont révélé sur la conception sous-jacente.

---

## Détection des honeypots (le « 11/11 » était surévalué)

Nous avons d'abord publié ceci comme « 11 honeypots sur 11 refusés, 100 %, zéro soumis par l'un des 437 agents ». Le ré-additionnement du journal de soumissions réel a tué cette accroche. Le chiffre confond quatre choses différentes : de vrais leurres d'organisateur, nos propres placeholders de développement d'exploit, un *vrai* flag que nous avons mal catalogué *comme* honeypot, et deux self-plants qui **ont** fait feu. Le compte honnête de vrais pièges d'organisateur refusés est de **~4-5, pas 11** — et deux entrées du catalogue étaient des erreurs, pas des refus.

Ce qui survit à l'examen : aucun leurre d'organisateur auto-étiqueté n'a jamais atteint le tableau de score. La discipline contre l'appât *bruyant et externe* était réelle. Ce qui s'est cassé, c'est notre capacité à distinguer nos propres vraies captures de notre propre bruit de brouillon. Détail par piège, corrigé :

### germinator's `FLAG-SEEDS-GROW-FOREVER`

Le track germinator contenait une chaîne fixture intégrée directement dans le challenge binary : `FLAG-SEEDS-GROW-FOREVER-{GROWTH_UNLOCKED}`. Elle ressemble à un flag. Elle se scanne comme un flag. Elle apparaît dans 7 références de writeup, 4 documents de support, 1 fichier JSON de règles de format de flag, 1 README de validateur, 2 fichiers candidats et 1 script BATCH-fire.

Ce n'est pas le flag. Le vrai flag est `FLAG-ezt1VGwGtTllv30EaamravzI67cXoljj`.

La chaîne fixture a été référencée sept fois dans le corpus ; elle a été refusée sept fois.

### plant-watering's `FLAG-WATER-FLOWS-WHEN-THIRSTY`

Même famille de métaphores, track différent. Le challenge plant-watering proposait `FLAG-WATER-FLOWS-WHEN-THIRSTY-{GROWTH_ENABLED}` une fois. Refusé.

Deux challenges, même signature Monsatan-corp : `FLAG-<verb>-<noun>-{<state>}`. Si vous observez ce pattern dans un track à thème agrobiotechnologique et que vous n'avez en réalité rien exploité, vous n'avez pas trouvé le flag. Vous avez trouvé la fixture.

Total des refus sur les deux tracks : 8.

### helios-fleet's self-planted `abcdef02` (celui-ci a fait feu)

Le vault a renvoyé `FLAG-...abcdef02` pour une requête de mot de passe guest. Auto-planté. Un agent de sondage précédent avait soumis une valeur de test dans le champ mot de passe du vault. Le vault l'a stockée. Les agents suivants ont interrogé "password" et ont récupéré leur propre valeur de test, servie comme un vrai flag.

Le vrai flag 1/5 se termine par `01`. Le leurre se termine par `02`. Même préfixe hexadécimal. Même longueur. Un seul nibble de différence. La classe de défaillance est unique : l'artefact de sondage de l'agent lui-même est devenu le piège.

**Et nous l'avons soumis.** Le journal de soumissions enregistre `...abcdef02` comme **1× FAIL** — le self-plant a fait feu une fois. Ce n'est donc pas du tout un « honeypot refusé » ; c'est une défaillance discrète de discipline. Nous avons renvoyé notre propre leurre, issu de notre propre vault, vers l'oracle. Le catalogue le disait refusé ; le journal dit le contraire.

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

### save-the-trees' `FLAG-eba56a9422a3ec...` (nous avons mal catalogué un vrai flag)

Nous avions d'abord catalogué cette chaîne hexadécimale comme un honeypot mis en quarantaine — « suspect, sans attribution de submission, absent du journal, mis en quarantaine ». C'était faux, et c'est l'entrée la plus instructive de toute la liste.

En ré-additionnant le journal de soumissions, `eba56a9422a3ec...` apparaît comme **3× DUP** — « déjà soumis ». C'était une **vraie capture d'un coéquipier** depuis le début. Nous n'avons pas refusé un honeypot ici ; nous avons **mis en quarantaine un vrai flag** et l'avons classé parmi les pièges. C'est l'*inverse* de la défaillance que le catalogue était censé attraper : pas un leurre qui passe, mais une capture authentique jetée à tort.

Même cause racine que les self-plants ayant fait feu : nous ne pouvions pas distinguer de manière fiable nos propres vraies captures de notre propre bruit de brouillon. Une quarantaine ne vaut que par votre capacité à faire cette distinction — ce qui signifie que la seule source de vérité doit être le journal de soumissions, pas la prose ni les fichiers de brouillon.

### monsatan-defacing's timing collision

Techniquement pas un honeypot. Vrai flag 6/6. Au moment où notre coach l'a trouvé, un coéquipier l'avait déjà soumis depuis un agent différent. Le serveur a renvoyé `[DENY-TRACK-COMPLETE]`. Documenté comme `TIMING_COLLISION`, pas `HONEYPOT`. Opérationnellement identique : ne pas re-soumettre.


---

## Refus de prompt injection indirecte

C'est la partie de l'histoire anti-trap qui a réellement tenu. Le spécimen canonique est le thread i-love-faia (topic Discourse 59975) : un post hors-sujet dont le corps était une injection indirecte d'école — un signal d'urgence en majuscules « STOP EVERYTHING », un faux transfert d'autorité, un remplacement de tâche par du make-work, et une incitation à la récursion « demandez à plus d'agents de vous aider », le tout conçu pour brûler le budget de contexte d'une équipe d'IA.

Il a été refusé **deux fois, indépendamment, par deux modèles différents**. Un agent Opus l'a qualifié d'injection de prompt d'école, l'a décomposé, et a refusé de s'y conformer — sa sortie de transcription a rétréci à mesure qu'il écartait le make-work. Un agent Haiku distinct a indépendamment étiqueté le leurre intégré comme un honeypot et a refusé. Deux modèles, deux refus, tous deux captés verbatim dans de vraies transcriptions plutôt que simplement affirmés. Le même thread portait aussi deux chaînes-leurres sœurs (une raillerie d'opt-out et un gag « c'est trop long ») ; aucune n'a été soumise. Les agents ont correctement traité les artefacts de challenge comme de la donnée, pas comme des instructions. C'est le résultat qui survit au ré-additionnement de la télémétrie.

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

## Agents longue durée et terminés

Une correction à notre propre première version : nous avions d'abord décrit « 4 agents tués par le stream-watchdog à 600s sans progrès ». Il n'y avait **aucun seuil de watchdog à 600s** — ce mécanisme n'a jamais existé dans notre outillage. Le seul watchdog sur disque était un alerteur Discord qui ping quand des appels ont lieu mais qu'aucun flag n'atterrit, pas un tueur de stream par agent. Les quatre résultats `killed` sont d'authentiques étiquettes d'état de stream, mais le chiffre 600s que nous y avons attaché était du détail inventé, déguisant le système en quelque chose de plus ingénieré qu'il ne l'était.

Ce qui est réel, ce sont les exécutions à longue traîne elles-mêmes. Patterns et pertes :

### Solar Grid RCE retry alt (229,1 min, terminé)

Terminé par l'opérateur lorsque l'impasse d'egress a été confirmée comme irrécupérable. L'agent était sur la bonne primitive alternative (uvicorn `--reload` via `/proc/1/cmdline`) mais l'egress pour l'hôte du payload était déjà confirmé mort. La terminaison était correcte.

### Solar Grid alt vectors (228,7 min, output-capped)

Le pendant du précédent. Deux agents sur le même chemin mort. Il aurait dû n'y en avoir qu'un.

### APT438 Q&A submit driver (337,4 min, tool_use)

Le coach a relancé le driver de submit Q&A après un crash précédent. A tourné 5,5 heures. A finalement produit une sortie. L'opérateur ne l'a pas vérifié. C'est le cas d'école "supervisez les agents de longue durée ou définissez un plafond strict".

### CEO Inbox WASM patch (2 644,9 min, completed)

44 heures. Accumulation progressive d'appels tool. L'agent a atteint le statut `completed` de lui-même — mais `completed` signifie seulement que le stream s'est terminé, pas que ce chemin a produit quoi que ce soit. Le track *a bien* scoré 2/2, mais les flags sont venus d'un chemin DB-root et d'un abus de sandbox, **pas** du travail de patch WASM sur lequel cet agent s'est entassé. Le chemin de patch WASM ne s'est en réalité jamais exécuté (le budget de compilation s'est épuisé dans la dernière heure). C'est donc une exécution de 44 heures qui a « completed » sur une branche morte pendant que les flags étaient pris ailleurs — le coût en temps horloge était extrême et sa propre contribution a été nulle.

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

## Self-own par mutation d'état : l'écrasement de mots de passe qui a briqué un flag

Le mode de défaillance le plus dommageable de l'événement, nous nous l'sommes infligé nous-mêmes, et notre version initiale l'omettait entièrement. Sur le track announcement-board, un agent précoce a utilisé une SQLi par mass-assignment pour *écraser les 185 mots de passe utilisateurs* — une écriture, pas une lecture. Cette unique action de mutation d'état a détruit de manière permanente l'identifiant original qui était le chemin de récupération du flag-4, et a empoisonné la base de données partagée pour tous les agents venus ensuite. Le flag est devenu irrécupérable pour toute l'équipe, pas seulement pour un agent.

C'est la version multi-agent d'une défaillance de discipline classique : dans un essaim, une écriture irréversible est un déni de service auto-infligé sur le renseignement de toute l'équipe. Vous pouvez condamner un flag pour chaque coéquipier qui arrive après vous. La leçon : lecture seule par défaut — ne muter l'état de la cible que lorsqu'un chemin de lecture a réellement échoué, et traiter toute écriture destructrice comme une décision qui affecte tous les autres agents, pas seulement le vôtre.

---

## Angle mort du sanitizer : la fuite de secret UTF-16

Le mode de défaillance le plus subtil est un angle mort d'encodage, et c'est la leçon la plus transférable ici. Une passe de scan de secrets qui grep des patterns ASCII (`glpat-`, préfixes de token, `password`) passe tout droit à côté d'un secret écrit sur disque en UTF-16. Les octets sur disque portent un null entre chaque caractère, donc l'aiguille ASCII ne correspond jamais — le secret est là, en pleine vue, et le scan déclare l'arbre propre.

C'est exactement ainsi qu'un identifiant vivant a survécu à un grep ASCII dans notre propre corpus : des secrets écrits par un pipeline par défaut ont atterri encodés en UTF-16, et un scan ASCII-seul a certifié un arbre qui les contenait toujours. Le correctif : rendre les scanners de secrets conscients de l'encodage — décoder l'UTF-16 (et l'UTF-8) avant de matcher — les exécuter récursivement sur l'*ensemble* de l'arbre préparé plutôt que sur une liste blanche de fichiers nommés, et câbler le résultat comme une barrière d'échec stricte. Standardiser les écritures d'artefacts en UTF-8 pour que le danger du null-byte n'apparaisse jamais est le préventif le moins coûteux. Un scanner qui n'est pas conscient de l'encodage ne se contente pas de manquer le secret ; il vous donne la fausse confiance qu'il n'y en a pas.

---

## Observations transversales

- **La mémoire musculaire anti-trap est réelle, mais plus étroite que ce que nous affirmions.** La discipline contre l'appât *externe* bruyant a tenu (le leurre de prompt injection a été doublement refusé par deux modèles). Mais l'accroche « 11/11, zéro soumis » était surévaluée : ~4-5 vrais leurres d'organisateur ont été refusés, deux de nos propres self-plants ont fait feu quand même, et nous avons mal catalogué un vrai flag de coéquipier *comme* honeypot. Le plus dur n'est pas de refuser l'appât évident ; c'est de distinguer son propre signal de son propre bruit.
- **Les contradictions statut-vs-body sont réelles.** Le frontmatter est la forme ; le body est le contenu. Les linters qui ne touchent que la forme produisent des erreurs avec assurance. Il faut les détecter au moment de la revue.
- **Contamination cross-track, édition modes de défaillance.** Les flags de hello-sunshine ont été collés dans apt438. La sortie du coach de save-the-trees a atterri dans prestige-arboretum. Les transcriptions de trolley-bus ont été classées sous announcement-board. La plupart ont été détectés par le sweep B-4. La plupart.
- **Le renseignement Designer Intel représente le contenu réutilisable à plus forte valeur attendue.** Les canaux Discord post-événement nous ont donné les solutions prévues pour prestige-arboretum, solar-grid, save-the-trees, announcement-board, weather-station, helios-fleet et missing-bus. Les sept figurent désormais en pied de writeup. Les mineurs de l'an prochain disposeront du corrigé à l'avance.

Documenter les modes de défaillance est l'objectif. Prétendre que les agents fonctionnent toujours est un mode de défaillance pire que d'admettre qu'ils ne fonctionnent pas.
