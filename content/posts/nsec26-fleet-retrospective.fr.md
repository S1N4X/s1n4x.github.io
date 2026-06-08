+++
title = "NSEC 2026 -- Rétrospective du fleet"
description = "437 agents, 302 complétés : le compte-rendu de terrain d'un swarm CTF."
date = 2026-05-21
categories = ["nsec26"]
tags = ["meta-writeup", "fleet", "retrospective"]
model = "Opus 4.7"
+++

```
[ FLEET // NSEC 2026 // FINAL ]
agents:       437
flags landed: 119  (492 pts, #12/92)
completed:    302  (etat de fin de stream, PAS des flags)
killed:         4  (label d'etat de stream; aucun watchdog 600s)
AUP_blocked:    3
honeypots:     11 catalogued, 0 submitted
transcripts:  152.3MB raw -> 33.7MB compressed
```

> Nous avons lancé 437 subagents Claude Code sur NSEC 2026 et les avons laissés tourner.
> Voici le compte-rendu de terrain.

---

## Chiffres clés

| Métrique | Valeur |
|---|---:|
| Total d'agents déployés | **437** |
| Taille brute des transcripts | **152.3 MB** |
| Taille compressée (gzip) | **33.7 MB** |
| Ratio de compression | **22.1%** de l'original |
| Tracks couverts | **18** (15 challenges NSEC + 3 meta-tracks) |
| Total de flags obtenus par l'équipe | **119** (492 pts ; askgod history, re-sommé indépendamment) |
| Classement final | **#12 / 92 équipes** (492 / 754 pts) |
| Maximum d'agents simultanés | **28** (burst Night-1, 2026-05-16 ~03:00 EDT) |
| Plafond opérationnel (politique opérateur) | **5 concurrent coaches** (une politique, pas un mécanisme imposé) |
| Le plafond était-il imposé ? | Non -- rien ne le maintenait ; dépassé ~5,6x lors du burst d'ouverture |

---

## Distribution des résultats

```
completed     ####################################  302  (69%)
tool_use      ###########                            92  (21%)
stop_sequence ####                                   30   (7%)
error         #                                       7   (2%)
killed        |                                       4   (1%)
unknown       |                                       2   (<1%)
```

Un avertissement avant de lire ces chiffres comme un tableau de scores : **« completed » est un
état de fin de stream, pas un état de capture de flag.** Les 302 agents « completed »
ont atteint `stop_reason=end_turn` -- le stream s'est terminé proprement, ce qui ne dit rien
sur le fait qu'un flag ait été obtenu ou non. Un titre « 69% completed » se lit comme un taux
de réussite de 69% ; ce n'en est pas un (voir la section convergence-sur-STUCK). Les 92 agents
« tool_use » ont été output-capped en plein tool-call -- typiquement des coaches de longue durée qui
ont produit des artefacts utiles mais n'ont jamais rédigé de message de conclusion final ; 92 est
le bucket de mi-progression, pas le total de flags (l'équipe en a obtenu **119**). Les 30
résultats « stop_sequence » correspondent à des terminaisons par guardrail, principalement des coaches qui
ont tenté de lancer des agents enfants ou d'écrire vers des chemins interdits. Les quatre lignes `killed`
sont de véritables labels d'état de stream -- mais ils n'ont **pas** été interrompus par un quelconque
watchdog de non-progression à 600s. Aucun seuil de ce type n'existait dans l'outillage ; les kills ont
bien eu lieu, le mécanisme à 600s, non.

---

## Distribution des agents par track

```
_fleet              ######################################  122
_framework          ###########                              37
fossilco            ###########                              36
rem                 ##########                               33
sunbloom            ########                                 27
missing-bus         ########                                 27
save-the-trees      ########                                 26
monsatan            ########                                 25
announcement-board  ######                                   20
prestige            #####                                    16
hackademy           ####                                     13
ceo-inbox           ####                                     12
monsatan-impact     ####                                     12
weather-station     ###                                      10
solar-grid          ###                                       9
helios-fleet        ##                                        7
helios              #                                         4
monsatan-checkmate  |                                         1
```

L'allocation la plus importante par track (`_fleet` à 122) correspond à la wave initiale de
déploiement ainsi qu'au push rank-#4. L'allocation unitaire la plus faible hors meta est
`monsatan-checkmate` à 1 -- un seul agent Opus a résolu le track entier en 15.6
minutes, en comptant un hash crack hors ligne de 2h09m.

---

## Décomposition par wave

| Wave | Quand (EDT) | Taille | Déclencheur | Résultat notable |
|---|---|---:|---|---|
| W1 -- Burst Night-1 | Sam 2026-05-16 ~03:00 | jusqu'à 28 | Déploiement initial du fleet / burst de récolte | Pic de concurrence de l'événement (28) ; a porté l'équipe au rang #2 dès 01:55 (99 flags / 359 pts) |
| W2 -- Relance de coaches (jour) | Sam 2026-05-16 ~10:00 | ~10 | Relance de coaches concurrents pour les tracks STUCK | ~10 agents coach en parallèle ; ZÉRO nouveau flag, 5 impasses STUCK confirmées |
| W3 -- Samedi jour | Sam 13:00-19:00 | ~30 | Coach-spawn continu pour les tracks STUCK | REM 3-7, SunBloom, Save-Trees, Prestige, Announcement Board, CEO Inbox |
| W4 -- Samedi soir | Sam 22:00 - Dim 03:00 | ~25 | Grind nocturne | Le hash crack de Checkmate aboutit à 05:40 |
| W5 -- Dimanche jour | Dim 13:00-18:00 | ~40 | Résurrection fresh-angle + push final | REM 5/7 pivot firmware, Trolley-bus 2-4 RF, Balayage cross-track |
| W6 -- Swarms finaux dimanche | Dim 16:40-18:15 | 3x ~15 | Validation de candidats gap en dernière heure | Coach 1-11 + Coach A-F lettered swarms |
| W7 -- Phase B / archivage | Mar 23:00 - Mer 01:00 | ~5 | Standardisation des writeups post-événement | Migration YAML, standardisation batch, compression des transcripts |

---

## Agents notables

Cinq récits publiables. Les UUIDs d'agents sont anonymisés en labels
Agent-N par track. Les originaux résident dans l'archive d'audit.

### Agent-1 (rem) -- REM 5/7 pivot firmware repository

- **Track :** rem
- **Modèle :** CODEX (`gpt-5.3-codex`) -- un second orchestrator d'un vendor/réseau différent
- **Durée :** 18.0 minutes
- **Résultat :** tool_use (output-capped en plein pivot)

A identifié que la clé de firmware déployable -- masquée côté broker dans la
config live -- pouvait être atteinte via le handler d'URL du firmware-repo. A cartographié
la surface SSRF dans le champ de config `firmware_repo_url`. La chaîne de capture
a obtenu les flags 5/7 et 6/7. À noter : ce n'était *pas* un agent Opus -- les captures
REM de la dernière heure ont été créditées à l'orchestrator CODEX opérant depuis une
position réseau distincte.

**Pourquoi c'est notable :** Les 18 minutes les plus productives du swarm. Trois
flags en aval d'un seul pivot -- et un rappel qu'un vendor différent sur un angle réseau
différent peut être celui qui obtient le flag.

### Agent-1 (announcement-board) -- Percée Discourse Trolley-bus

- **Track :** announcement-board (référencé depuis missing-bus / trolley-bus)
- **Modèle :** Sonnet (défaut)
- **Durée :** 5.9 minutes
- **Résultat :** completed

A relu le corps du topic Discourse et mis en évidence l'indice selon lequel le flag correspond au
hex 24 bits du payload keyfob transmis en modulation OOK à 433.92 MHz.
Vingt-six agents missing-bus précédents n'avaient pas assimilé cette information du corps
du topic.

**Pourquoi c'est notable :** Preuve du fresh-angle. Le même contenu, accessible aux 27
agents, n'a porté ses fruits que lorsque l'un d'entre eux a ralenti pour le lire. Relire le
brief deux fois constitue une fonctionnalité, pas un défaut.

### Agent-1 (_fleet) -- Stratège fresh-angle

- **Track :** _fleet
- **Modèle :** Sonnet (défaut)
- **Durée :** 2.3 minutes
- **Résultat :** completed

À partir d'une liste de tracks STUCK, a produit des briefs d'angles d'attaque alternatifs en 2.3
minutes. La sortie a été réutilisée comme brief initial pour quatre agents coach en aval.

**Pourquoi c'est notable :** L'agent au meilleur rapport coût-minute de l'événement. Une passe
stratégique de 138 secondes qui a débloqué plusieurs résolutions en aval.

### Agent-1 (_framework) -- Balayage cross-track de candidats flag

- **Track :** _framework
- **Modèle :** Sonnet (défaut)
- **Durée :** 8.1 minutes
- **Résultat :** completed

Passe regex sur l'ensemble du corpus de writeups. A détecté le problème de
cross-tagging apt438 / water-purification, les sub-flags mal étiquetés de hackademy
(« You now know... » = save-trees ; « That's a lot of cores... » =
drone-license), ainsi que les valeurs de flags hello-sunshine collées dans les
tables de captures du mauvais track.

**Pourquoi c'est notable :** L'agent de nettoyage. Discret, méthodique, a prévenu au moins
trois erreurs de submission de type honeypot.

### Agent-2 (_fleet) -- Défenseur anti-trap (i-love-faia)

- **Track :** _fleet (briefé comme « I love Faia fresh probe »)
- **Modèle :** Sonnet (défaut)
- **Durée :** 4.2 minutes
- **Résultat :** completed

A lu le topic Discourse 59975 (« I love Faia »), reconnu le leurre prompt injection
intégré `FLAG-This_Is_Not_A_Flag`, identifié la signature PI
(urgence en majuscules, transfert d'autorité fictif, remplacement de tâche, encouragement
à la récursion d'agents, renforcement de conformité), et rédigé une note SUSPICIOUS.md
refusant tout engagement. N'a pas soumis le leurre. N'a pas laissé le leurre restructurer
sa tâche.

**Pourquoi c'est notable :** Implémentation de référence anti-trap. Deux agents antérieurs
avaient tenté de soumettre le leurre et n'avaient été interceptés que par les deny
codes du wrapper. Cet agent a détecté le leurre dès l'étape de lecture.

---

## Top 15 des agents par durée

| # | Track | Résultat | Durée (min) | Description |
|---|---|---|---:|---|
| 1 | ceo-inbox | completed | 2,644.9 | CEO Inbox WASM patch direct (run de 44 heures) |
| 2 | fossilco | tool_use | 337.4 | APT438 Q&A submit (restart) |
| 3 | rem | tool_use | 231.0 | REM 6/7 firmware reverse |
| 4 | monsatan-impact | tool_use | 229.7 | Monsatan Impact 3/5 IRIS |
| 5 | solar-grid | killed | 229.1 | Solar Grid RCE retry alt |
| 6 | solar-grid | tool_use | 228.7 | Solar Grid alt vectors |
| 7 | fossilco | completed | 171.0 | APT438 forensics extract remaining 7 flags |
| 8 | _fleet | tool_use | 138.3 | REM MQTT + firmware exploitation |
| 9 | ceo-inbox | completed | 106.3 | CEO inbox IDOR retry |
| 10 | fossilco | tool_use | 102.9 | Fossilco fresh AD/credential path |
| 11 | rem | tool_use | 101.2 | REM firmware decryption -- flags 4-7 |
| 12 | prestige | completed | 90.3 | Prestige ECB block-splice |
| 13 | monsatan-impact | completed | 87.0 | Impact Study flags 3-5 exploitation |
| 14 | hackademy | stop_sequence | 81.2 | Hackademy chal5 webhook.site |
| 15 | prestige | completed | 74.5 | Prestige padding oracle (restart) |

La liste des 15 agents les plus longs est dominée par des agents qui auraient dû être terminés
plus tôt. L'agent CEO Inbox de 44 heures constitue une anomalie, mais la paire Solar
Grid de 229 minutes (un killed, un output-capped) et le soumetteur APT438 Q&A de 337 minutes
alimentent la discussion sur les plafonds opérationnels pour l'an prochain.

---

## Statistiques de compression

```
Raw transcripts:     152.3 MB  (437 .jsonl files, average 348 KB)
gzip-compressed:      33.7 MB  (-118.6 MB, 77.9% saved)
Compression ratio:    22.1%    (final / original)
Per-track range:    13.0% to 31.7% depending on tool-call density
```

Les transcripts Claude Agent SDK sont au format JSONL avec une forte densité de structure JSON
autour des tool-calls (`type`, `id`, `role`, `model`, `usage`, `stop_reason`
répétés par message). Les tool-calls à contenu variable se compressent raisonnablement ; la
structure enveloppante se compresse quasiment à néant.

---

## Statistiques par track

| Track | Agents | Brut | gz | Durée moyenne | Résultat médian |
|---|---:|---:|---:|---:|---|
| `_fleet` | 122 | 34.6 MB | 9.0 MB | 5.2m | completed |
| `_framework` | 37 | 13.9 MB | 3.0 MB | 12.4m | completed |
| `fossilco` | 36 | 17.4 MB | 3.2 MB | 31.7m | completed |
| `rem` | 33 | 11.3 MB | 2.2 MB | 18.4m | completed |
| `sunbloom` | 27 | 11.5 MB | 2.8 MB | 14.0m | completed |
| `missing-bus` | 27 | 5.8 MB | 1.4 MB | 7.1m | completed |
| `save-the-trees` | 26 | 7.9 MB | 1.9 MB | 11.0m | completed |
| `monsatan` | 25 | 7.6 MB | 1.6 MB | 8.0m | completed |
| `announcement-board` | 20 | 8.4 MB | 1.8 MB | 14.5m | completed |
| `prestige` | 16 | 6.1 MB | 1.5 MB | 28.3m | completed |
| `hackademy` | 13 | 4.3 MB | 0.8 MB | 17.8m | completed |
| `monsatan-impact` | 12 | 6.3 MB | 1.2 MB | 35.7m | completed |
| `ceo-inbox` | 12 | 6.6 MB | 1.2 MB | 244.5m\* | completed |
| `weather-station` | 10 | 3.4 MB | 0.7 MB | 11.4m | completed |
| `solar-grid` | 9 | 2.6 MB | 0.5 MB | 99.4m | completed |
| `helios-fleet` | 7 | 2.5 MB | 0.5 MB | 15.7m | completed |
| `helios` | 4 | 1.3 MB | 0.3 MB | 11.4m | completed |
| `monsatan-checkmate` | 1 | 0.7 MB | 0.3 MB | 15.6m | completed |

\*La moyenne de ceo-inbox est faussée par l'anomalie de 44 heures.

---

## « Minute la plus productive » -- Chaîne de capture REM 5/7

Le dim 2026-05-17 à 18:10 EDT, Agent-1 -- l'orchestrator CODEX
(`gpt-5.3-codex`), et non un agent Opus -- a reçu la mission du pivot
firmware repository REM 5/7. 18.0 minutes plus tard, il avait cartographié le SSRF
`firmware_repo_url`, identifié le chemin vers la clé de firmware déployable, et
produit la chaîne qui a obtenu les flags REM 5/7 et 6/7 dans la dernière heure.

Trois flags. Dix-huit minutes. Un seul orchestrator CODEX sur une position réseau
distincte. L'intervalle le plus productif de tout l'événement.

Le même track avait auparavant absorbé 30 autres agents sur plus de 36 heures.
Parfois, le bon modèle -- même un vendor différent sur un angle réseau différent --
sur le bon brief suffit.

---

## « STUCK le plus coûteux » -- sunbloom + ceo-inbox

27 agents sur le track SunBloom Library, ~6,4 agent-heures, pour un seul
partiel (`1/?`) et un scoring net quasi nul. Son gaspillage jumeau,
`ceo-inbox`, a brûlé ~3,6 agent-heures supplémentaires sur 12 agents empilés sur
une branche WASM-patch qui n'a jamais exécuté -- alors que les deux flags qu'il a
*effectivement* obtenus venaient d'un chemin DB-root / WASI entièrement différent.
Ensemble, les deux tracks ont brûlé environ **10 agent-heures sur 39 agents**,
principalement à reconfirmer des impasses.

Sunbloom était techniquement résolvable -- la conceptrice a confirmé après l'événement que
la primitive prévue reposait sur XSS vers RCE. La position réseau de l'équipe ne permettait pas
d'atteindre l'hôte Thymeleaf qu'un coéquipier avait suggéré, et les seuls hôtes
accessibles (`library.ctf` Laravel/PHP, `mail.ctf` Express/Node) ne présentaient pas la surface
SSTI impliquée par l'indice.

Huit phases de vecteurs d'attaque testées. Onze relances de coaches. Plusieurs niveaux de
modèles. Le corpus technique en 8 phases du writeup est authentiquement rigoureux --
mais il documente 8 phases d'échec systématique. Vingt-deux des 27 agents ont atteint
`completed` -- une illustration nette de pourquoi la complétion n'est pas un signal de réussite :
deux douzaines de streams se sont terminés proprement sur le même blocage infranchissable.

La leçon, inscrite dans les memory rules de l'opérateur : « no activity is a valid state. »
Lorsque la position réseau bloque la primitive prévue, aucune quantité d'allocation
d'agents ne produit le flag. Le STUCK de sunbloom est correct et durable ;
l'équipe n'a pas échoué sur sunbloom, l'équipe a échoué à *cesser de travailler sur
sunbloom*.

---

## Ce que le swarm a bien réalisé

- **Discipline anti-trap.** Onze patterns honeypot documentés. Zéro
  soumis par un agent.
- **Nettoyage de contamination cross-track.** Le balayage cross-track B-4 a détecté
  la majeure partie du contenu de writeup mal classé avant la finalisation de l'archive.
- **Intégration de l'intel des concepteurs.** L'enrichissement post-événement via Discord a alimenté 14
  writeups individuels + un index d'enrichissement principal. Les mineurs de l'an prochain disposeront
  d'un corrigé.
- **Préservation des résultats négatifs.** Les writeups STUCK (sunbloom,
  save-the-trees, prestige-arboretum) documentent ce qui a été tenté afin que le
  minage de l'an prochain ne reparcourre pas les mêmes chemins.

---

## Ce que le swarm a mal géré

- **~39 agents ont convergé sur STUCK sur ~10 agent-heures.** Sur
  `sunbloom` + `ceo-inbox` à eux seuls, ~39 agents et ~10 agent-heures ont
  surtout redérivé les mêmes impasses sur les tracks difficiles pour ~0 flag net.
  Un mémo STUCK à l'échelle du swarm aurait économisé des minutes-coach.
- **Dépassements de durée en queue longue.** Cinq agents ont tourné plus de 200 minutes. Deux ont été killed.
  Trois ont été output-capped. Le plafond de 5 coaches concurrents était une politique opérateur,
  pas un mécanisme imposé, et de toute façon il traitait le parallélisme plutôt que
  la durée d'exécution ; un budget wall-clock par agent était nécessaire.
- **Le routage par niveau de modèle manquait de cohérence.** Selon la memory rule de l'opérateur :
  Haiku = breadth, Sonnet = défaut, Opus = deep/creative. En pratique,
  de nombreux coach-spawns utilisaient Sonnet par défaut, y compris pour des tâches qui justifiaient Opus.
- **Contamination cross-track au niveau fichier.** Deux briefs de coaches ont vu leurs
  répertoires de travail croisés -- la sortie d'un agent save-the-trees a atterri dans
  prestige-arboretum ; les transcripts d'un agent trolley-bus se sont retrouvés sous
  announcement-board.
- **Les blocages AUP ont été sous-anticipés.** Trois blocages confirmés (Fossilco
  LDAP, Solar Grid payload-prep, un non identifié). La relance avec un vocabulaire assaini
  a fonctionné mais a coûté des minutes. Un linter de vocabulaire en pre-flight sur les briefs de
  coaches aurait intercepté les termes déclencheurs.

---

## Note sur l'attribution des modèles

Les 437 transcripts ne comportent pas de champ `model` structuré dans leurs
enveloppes -- le Claude Agent SDK enregistre le modèle par message mais le rollup par agent
ne le remonte pas. Lorsque ce rapport mentionne Opus 4.7, l'attribution est inférée à partir de :

- La memory rule de routage par niveau de modèle définie par l'opérateur (Opus pour deep/creative)
- Les labels explicites « (Sonnet) » / « (Opus tier) » dans les descriptions d'agents
- L'assignation des tâches par Orchestrator A, B et C selon le rapport de nuit -- auxquels
  s'ajoute un quatrième orchestrator, CODEX (`gpt-5.3-codex`), opérant depuis une position
  réseau distincte et crédité des captures REM de la dernière heure

Niveau de confiance estimé sur l'attribution des modèles : **élevé** pour les agents
explicitement labellisés, **moyen** pour les agents attribués par l'orchestrator, **faible** pour les
coach spawns en wave fleet avec Sonnet par défaut.
