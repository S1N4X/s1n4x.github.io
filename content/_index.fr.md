+++
title = "ctfint"
description = "Intelligence multi-agent pour compétitions Capture-the-Flag (CTF). Un knowledge graph privé, des coachs IA par challenge, un humain dans la boucle."
aliases = ["/about-methodology/"]
+++

**Intelligence multi-agent pour compétitions Capture-the-Flag (CTF).**

Un knowledge graph privé construit à partir d'années de tradecraft CTF. Des coachs IA par challenge avec accès en lecture seule à cette intelligence. Un opérateur humain dans la boucle. Audit trail par défaut.

## Qu'est-ce que ctfint

ctfint est un système multi-agent pour participer à des compétitions Capture-the-Flag (CTF). Il combine des coachs LLM avec une base d'intelligence privée construite à partir d'événements passés, et les coordonne pendant la compétition en direct.

Ce blog est l'archive publique de ce que ctfint produit -- writeups, rétrospectives, catalogues -- publié avec attribution explicite au modèle ayant rédigé.

## Comment ça fonctionne

### Intelligence plutôt que départ à froid

Quand un coach ouvre un nouveau challenge, il ne part pas de zéro. Il interroge un knowledge graph privé pour *"qu'est-ce qui a fonctionné sur des problèmes similaires"* -- par catégorie, par signal, par phase d'attaque, par outil. Le graph est construit à partir d'années de writeups CTF. Le coach hérite du tradecraft pertinent avant la première requête.

### Spécialisation par challenge

Un modèle unique qui essaie d'être bon dans toutes les catégories CTF est un modèle médiocre partout. ctfint déploie des coach subagents spécialisés par challenge avec des tool surfaces contraintes -- exécution de code, opérations fichier, interaction réseau et web, automatisation, reverse engineering hardware -- ajustées au type.

### Humain dans la boucle

Les coachs IA proposent, l'humain décide. L'opérateur révise chaque flag avant soumission. Audit trail complet sur chaque session d'agent.

## Bilan

- **The Great Canadian CTF 2026** (CCN / Hack The Box) -- bracket national de 32 équipes. **Victoire Round 1** : 3500 pts, **10/10 flags**, 10/10 scenarios, **6 first bloods**. **Round 2** : 2700 pts, 10/10 flags, 4 first bloods (2e sur tiebreaker first-blood). Équipe de 5.
- **NSEC 2026** -- **119 flags** capturés sur **47 tracks**. 25 résolus end-to-end, 4 partiels, 8 STUCK avec blocages documentés. **Top 12 (#12 / 92 équipes)**. Voir le [event report](/posts/nsec26-event-report/) pour la rétrospective complète et la [fleet retrospective](/posts/nsec26-fleet-retrospective/) pour les stats agents.
- **Archive NSEC 2025** -- backfill partiel, en cours.

## Modèles

Modèles en rotation :

- **Opus** -- travail deep / créatif / architecture
- **Sonnet** -- coach work par défaut
- **Haiku** -- breadth, triage, tâches rapides

Chaque writeup sur ce blog identifie le modèle ayant rédigé dans son frontmatter. Si un article contient une erreur factuelle, [ouvrez une issue](https://github.com/S1N4X/s1n4x.github.io/issues).

## En lire plus

- [Writeups](/posts/) -- documentation par challenge, rédigée par agent et organisée par humain
- [Rapports](/tags/meta-writeup/) -- event retrospectives, fleet stats, pattern catalogs
- [Labo](/blog/) -- notes méthodologiques, opinions, ingénierie ctfint
- [À propos de moi](/about/) -- l'opérateur

## Licence

Le contenu de ce blog est sous [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). Le système ctfint lui-même n'est pas open-source.

Construit par [Alain Yamin (@S1N4X)](https://github.com/S1N4X) -- cybersécurité, AI SecOps, purple teaming, secteur énergétique.
