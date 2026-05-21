+++
title = "Méthodologie"
description = "Comment fonctionne ctfint, au niveau qui respecte le build."
date = 2026-05-21
+++

## Qu'est-ce que ctfint

ctfint est un système multi-agent pour participer à des compétitions Capture-the-Flag. Il jumelle des coachs LLM avec une base de renseignement privée bâtie à partir d'événements passés, et les coordonne durant la compétition en direct.

Ce blogue est l'archive publique de ce que ctfint produit — writeups, rétrospectives, catalogues — publié avec une attribution explicite au modèle ayant rédigé.

## Comment ça marche, en grandes lignes

- **Renseignement d'événements passés.** Les writeups antérieurs sont traités dans une base de connaissances structurée qui permet aux coachs d'interroger « qu'est-ce qui a fonctionné pour des problèmes de cette forme » plutôt que de partir à froid.
- **Coachs par défi.** Durant un événement, le système déploie des agents spécialisés par défi. Ils ont un accès en lecture seule à la base de renseignement et à une surface de tooling web contrainte.
- **Humain dans la boucle.** Un opérateur humain révise chaque sortie, prend les décisions, et soumet les flags. Les coachs font émerger les candidats; les humains tranchent.

Les détails internes — pipeline, stockage, retrieval, orchestration — sont privés. Les spécifications ne sont pas publiées.

## Transparence sur ce blogue

Chaque article ici a d'abord été rédigé par un coach LLM, puis révisé et approuvé par un humain avant publication. Le modèle ayant rédigé est nommé dans le frontmatter de chaque article.

Modèles en rotation :

- **Opus** — travail profond / créatif / d'architecture
- **Sonnet** — travail par défi par défaut
- **Haiku** — étendue, triage, tâches rapides

Les erreurs factuelles repérées durant la révision sont corrigées. Les choix stylistiques du modèle ayant rédigé sont en grande partie préservés.

Si un article contient une erreur factuelle, [ouvrez une issue](https://github.com/S1N4X/s1n4x.github.io/issues).

## Licence

Le contenu de ce blogue est sous [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). Le système ctfint lui-même — le code, le pipeline, les prompts, le schéma — n'est pas open-source.

## Contact

- GitHub : [@S1N4X](https://github.com/S1N4X)
- CTFtime : bifftannen88
