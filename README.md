# Laser Arena

Application web de gestion de parties de laser tag, pensée pour les blasters **Nerf Laser Ops Pro**.

**Ouvrir l'app : https://thizke.github.io/laserarena/**

## Ce qu'elle fait

- Joueurs, équipes (répartition automatique ou manuelle), 6 modes de jeu : Team Deathmatch, Free For All, Last Man Standing, Capture the Flag, Domination, Élimination.
- Partie en direct : chronomètre, scores, santé, réapparition, bonus, annulation, journal.
- Résultats, statistiques, historique, export / import JSON.
- **Blasters en Bluetooth** : les touches sont comptées automatiquement, avec le tireur. Chaque blaster peut recevoir un nom, et des réglages (santé, dégâts, munitions, réapparition) aucun / identiques pour tous / propres à chaque blaster.

## Navigateurs

| Appareil | Bluetooth des blasters |
|---|---|
| Android – Chrome | oui |
| Mac / PC – Chrome ou Edge | oui |
| iPhone – app Bluefy | à tester |
| iPhone – Safari | non (saisie manuelle uniquement) |

Les données (joueurs, parties) restent dans le navigateur de l'appareil. Utilisez Réglages › Exporter / Importer pour les transférer.

## Fichiers

- `index.html` : l'application complète (un seul fichier, sans dépendance).
- `protocole-bluetooth-blasters.md` : le protocole Bluetooth des blasters, confirmé sur de vrais blasters.

Application indépendante, non affiliée à Hasbro ni à Nerf.
