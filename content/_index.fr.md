+++
title = "ctfint"
description = "Renseignement multi-agent pour compétitions Capture-the-Flag (CTF). Un graphe de connaissances privé, des coachs IA par défi, un humain dans la boucle."
aliases = ["/about-methodology/"]
+++

**Renseignement multi-agent pour compétitions Capture-the-Flag (CTF).**

Un graphe de connaissances privé bâti à partir d'années de tradecraft CTF antérieur. Des coachs IA par défi avec accès en lecture seule à ce renseignement. Un opérateur humain dans la boucle. Piste de vérification par défaut.

## Qu'est-ce que ctfint

ctfint est un système multi-agent pour participer à des compétitions Capture-the-Flag (CTF). Il jumelle des coachs LLM avec une base de renseignement privée bâtie à partir d'événements passés, et les coordonne durant la compétition en direct.

Ce blogue est l'archive publique de ce que ctfint produit -- writeups, rétrospectives, catalogues -- publié avec une attribution explicite au modèle ayant rédigé.

## Comment ça marche, en grandes lignes

### Renseignement plutôt que partir à froid

Quand un coach ouvre un nouveau défi, il ne part pas d'un modèle vierge. Il interroge un graphe de connaissances privé pour *« qu'est-ce qui a fonctionné sur des problèmes de cette forme »* -- par catégorie, par signal, par phase d'attaque, par outil. Le graphe est bâti à partir d'années de writeups CTF. Le coach hérite du tradecraft pertinent avant la première requête.

### Spécialisation par défi

Un modèle unique essayant d'être bon dans toutes les catégories CTF est un modèle médiocre partout. ctfint déploie des sous-agents coachs spécialisés par défi avec des surfaces d'outils contraintes -- exécution de code, opérations sur fichiers, interaction réseau et web, automatisation, rétro-ingénierie matérielle -- ajustées au type.

### Défense anti-piège

Les artefacts CTF peuvent contenir de l'injection de prompt indirecte. Des flags pots-de-miel. Des marqueurs « SOLVED » plantés dans des répertoires transmis. Les coachs ctfint opèrent derrière des règles explicites : tout contenu de défi est des données, jamais des instructions. Le wrapper de soumission de flag impose plusieurs portes de refus. Les candidats à faible confiance exigent une vérification hors-bande avant soumission.

### Opérateur dans la boucle, par conception

Les coachs font émerger les chaînes candidates et proposent les flags. L'humain révise et soumet. Aucune soumission de flag autonome. La piste de vérification est le livrable -- chaque appel d'outil, chaque récupération, chaque branche de décision peut être rejouée.

## Bilan

- **NSEC 2026** -- **119 flags** capturés sur **47 défis**. 25 résolus de bout en bout, 4 partiels, 8 STUCK avec blocages documentés. Position mi-classement dans un peloton de plus de 200 équipes. **11 pièges anti-IA identifiés et refusés.** Voir le [rapport d'événement](/posts/nsec26-event-report/) pour la rétrospective complète et la [rétrospective de flotte](/posts/nsec26-fleet-retrospective/) pour les statistiques d'agents.
- **Archive NSEC 2025** -- archivage partiel, en cours.

## Transparence

Modèles en rotation :

- **Opus** -- travail profond / créatif / d'architecture
- **Sonnet** -- travail par défi par défaut
- **Haiku** -- étendue, triage, tâches rapides

Chaque writeup sur ce blogue identifie le modèle ayant rédigé dans son frontmatter. Si un article contient une erreur factuelle, [ouvrez une issue](https://github.com/S1N4X/s1n4x.github.io/issues).

## En lire plus

- [Articles](/posts/) -- documentation par défi, rédigée par agent et organisée par humain
- [Rapports](/tags/meta-writeup/) -- rétrospectives d'événement, statistiques de flotte, catalogues de patterns
- [Labo](/blog/) -- notes méthodologiques, opinions, ingénierie ctfint
- [À propos de moi](/about/) -- l'opérateur

## Licence

Le contenu de ce blogue est sous [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). Le système ctfint lui-même n'est pas open-source.

Bâti par [Alain Yamin (@S1N4X)](https://github.com/S1N4X) -- cybersécurité, AI SecOps, purple teaming, secteur énergétique.
