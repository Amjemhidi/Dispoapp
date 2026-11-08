# Documentation du domaine

Comment les skills d'ingénierie doivent utiliser la documentation du domaine de ce dépôt quand elles explorent le code.

## Avant d'explorer, lire

- **`GLOSSARY.md`** à la racine du dépôt ;
- **`docs/adr/`** : lire les ADR qui concernent la zone sur laquelle on va travailler.

Si ces fichiers n'existent pas, **continuer sans rien dire**. Ne pas signaler leur absence et ne pas proposer de les créer d'avance. La skill `/domain-modeling` (appelée par `/grill-with-docs` et `/improve-codebase-architecture`) les crée au fur et à mesure, quand des termes ou des décisions sont réellement tranchés.

## Structure des fichiers

Dépôt à contexte unique :

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-exemple-de-decision.md
│   └── 0002-autre-decision.md
└── index.html
```

## Utiliser le vocabulaire du glossaire

Quand un résultat nomme un concept du domaine (titre de ticket, proposition de refactorisation, hypothèse, nom de test), utiliser le terme tel qu'il est défini dans `GLOSSARY.md`. Ne pas glisser vers des synonymes que le glossaire écarte explicitement.

Si le concept voulu n'est pas encore dans le glossaire, c'est un signal : soit on invente un langage que le projet n'utilise pas (à reconsidérer), soit il y a un vrai manque (à noter pour `/domain-modeling`).

## Signaler les conflits avec les ADR

Si un résultat contredit un ADR existant, le dire explicitement au lieu de passer outre en silence :

> _Contredit l'ADR-0007 (…), mais mérite d'être rouvert parce que…_
