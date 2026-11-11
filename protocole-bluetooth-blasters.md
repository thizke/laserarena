# Protocole Bluetooth des blasters Nerf Laser Ops Pro

Trouvé dans le code de l'app officielle (APK Android « Nerf Laser Ops Pro », Unity/Mono, fichier `Assembly-CSharp.dll`, classes `BlasterProfile`, `BlasterController`, `BlasterManager`, `BLECommandQueue`).
Statut : **confirmé sur deux vrais blasters le 11/11/2026** (connexion, lecture, réglage équipe/santé, départ de partie, tirs, touché, hors jeu, retour en jeu). Le n° du tireur est aussi confirmé : avec A réglé en n° 1 rouge et B en n° 2 bleu, B reçoit `30 00 01 02 08` (tireur équipe 0, n° 1) et A reçoit `30 01 02 02 08` (tireur équipe 1, n° 2). Un seul ordinateur a suivi deux blasters connectés en même temps.

Les deux blasters testés s'annoncent sous le nom « NerfV ». Écriture : sans réponse (write without response). Les valeurs sur 16 bits sont envoyées poids fort d'abord.

## Identifiants Bluetooth (BLE)

| Rôle | UUID |
|---|---|
| Service du blaster (la recherche de l'app filtre sur ce service) | `073e1435-85d1-455c-97cd-0b8262f20eac` |
| Messages du blaster vers l'app (Notify) | `073e1382-85d1-455c-97cd-0b8262f20eac` |
| Commandes de l'app vers le blaster (Write) | `073e1383-85d1-455c-97cd-0b8262f20eac` |
| Radio (déclarés, non utilisés pour le jeu) | `073e0756-…`, `073e0757-…` |

Format : messages bruts, sans chiffrement ni somme de contrôle. Premier octet = code (souvent un caractère ASCII), puis les valeurs. L'app envoie au plus une commande toutes les 0,1 s.

Équipes : `0` = équipe 1 (rouge), `1` = équipe 2 (bleu), `2` = chacun pour soi (violet).
Modèles : `0` Pistol, `1` Rifle, `2` Sidearm, `3` Sniper, `4` Uzi.

## Messages reçus du blaster

| Code | Contenu |
|---|---|
| `0x30` '0' **Touché** (confirmé) | [1] équipe du tireur, [2] **n° de joueur du tireur**, [3] dégâts, [4] santé restante. Exemple : `30 02 00 02 08`. Si le tireur est de la même équipe : soin (sauf en chacun pour soi). |
| `0x31` '1' | Munitions après rechargement : [1] (confirmé : `31 0a` juste après `52`) |
| `0x32` '2' | **Tir pendant une partie** : [1] = 2 (sens inconnu), [2] munitions restantes (confirmé) |
| `0x33` '3' | Équipe : [1] |
| `0x35` '5' | Caractéristiques (confirmé : `35 00 0a 02 02 01 00 0a 02 09 32 00 0a`) : [1] son, [2] munitions max (10), [3] dégâts (2), [4] temps de rechargement, [5] cadence, [6] modèle, [7] santé max (10), [9]/[10] nom, [12] durée hors-jeu |
| `0x38` '8' | Santé : [1] |
| `0x3A` ':' | Munitions : [1] |
| `0x3E` '>' | Hors jeu (santé à 0), début du délai : [1] = 1, [2] = 9 observés (sens à préciser) |
| `0x3F` '?' | Fin du délai : retour en jeu (observé 15 s après `3E`) |
| `0x40` '@' | État de partie : [1] morts, [2] munitions, [3] santé |
| `0x47` 'G' | Statistiques : [1–2] nombre de tirs (16 bits) |
| `0x48` 'H' | N° de joueur : [1] (0 par défaut à l'allumage, confirmé) |
| `0x49` 'I' | **Détente pressée** (envoyé à chaque pression, même hors partie ; très fréquent) |
| `0x4B` 'K' | Version du logiciel : [1] |
| `0x51` 'Q' | Batterie : [1–2] poids fort d'abord, en centièmes de volt probablement (`03 57` = 8,55 V ; `02 c6` = 7,10 V) |
| `0x52` 'R' | Rechargement |
| `0x54` 'T' | Statistiques sur un autre joueur |

## Commandes envoyées au blaster

| Code | Commande | Valeurs |
|---|---|---|
| `0x20` | Demander l'adresse | – |
| `0x33` | Demander l'équipe | – |
| `0x34` | Demander le modèle | – |
| `0x35` | Demander les caractéristiques | – |
| `0x38` | Demander la santé | – |
| `0x3A` | Demander les munitions | – |
| `0x40` | Demander l'état de partie | – |
| `0x41` 'A' | **Démarrer la partie** | délai en ms sur 2 octets (poids fort d'abord) |
| `0x42` 'B' | **Terminer la partie** | – |
| `0x47` | Demander les statistiques | – |
| `0x48` | Demander le n° de joueur | – |
| `0x4B` | Demander la version | – |
| `0x51` | Demander la batterie | – |
| `0x55` | Demander le modificateur de dégâts | – |
| `0x36` '6' | Régler toutes les caractéristiques | son, munitions max, dégâts, rechargement, cadence, santé, nom 1, nom 2, durée hors-jeu |
| `0x37` '7' | Activer le tir puissant | dégâts, coût en munitions, son |
| `0x39` '9' | Régler la santé | santé |
| `0x3B` ';' | Régler les munitions | munitions |
| `0x3C` '<' | Régler le temps de réapparition | durée |
| `0x3D` '=' | Régler l'équipe | équipe |
| `0x43` 'C' | Régler le pack de sons | n° |
| `0x44` 'D' | Régler le n° de joueur | n° |
| `0x46` 'F' | Régler le modèle | modèle |
| `0x49` 'I' | **Régler joueur + équipe** | n° de joueur, équipe |
| `0x4A` 'J' | Préparer la partie (12 octets) | joueur, équipe, délai hors-jeu, durée de partie, perte de santé progressive, etc. |
| `0x4C` 'L' | Durée hors-jeu | durée, incrément, maximum |
| `0x4D` 'M' | Modificateur de dégâts | – |
| `0x4E` 'N' | Jouer un son | – |
| `0x4F` 'O' | Son on/off | – |
| `0x50` 'P' | Forcer la couleur de la LED | couleur, durée en secondes |
| `0x53` 'S' | Activer le tir de soin | soin, coût en munitions, son |
| `0x5C` '\' | Désactiver le tir de soin | – |
| `0x5A` 'Z' | Simuler une touche | tireur, équipe, dégâts, tir puissant, tir de soin |
| `0x5B` '[' | Volume | volume |
| `0x56` / `0x57` | Effacer / sauvegarder une caractéristique (à éviter : modifie le blaster durablement) | – |
| `0x58` / `0x59` | Mode borne (base station) on / off | – |

## Séquence de l'app officielle à la connexion

1. Recherche des appareils qui annoncent le service `073e1435-…`.
2. Connexion, puis abonnement aux messages sur `073e1382-…`.
3. Envoi de `0x49` (joueur + équipe), `0x5B` (volume), `0x35` (caractéristiques), puis `0x51` (batterie).
4. Au compte à rebours : `0x4A` (préparation de partie) puis `0x41` (départ).
5. Pendant la partie : les messages `0x30` (touché), `0x31` (tir), `0x3E`/`0x3F` (hors jeu / retour) arrivent tout seuls.
6. Fin : `0x42`.

## Conséquence pour Laser Arena

Chaque blaster reçoit un **n° de joueur** unique via `0x49`. Chaque message « touché » contient le n° du tireur. L'app peut donc compter automatiquement **qui touche qui**, la santé et les éliminations. Il faudra un iPhone pour tous les blasters, avec plusieurs connexions Bluetooth en même temps. iOS en accepte généralement 6 à 10 selon le modèle, à vérifier.

## Noms d'origine des blasters

Le message `0x35` contient aux octets [9] et [10] deux indices (1 à 50) qui forment le nom donné par l'app officielle et gardé dans le blaster. 0 = pas de nom. Listes extraites de l'APK (`BlasterNameList`, ordre d'origine) :

- 1er mot : Air, Alpha, Atomic, Beta, Blazing, Blue, Cool, Cosmic, Crimson, Dark, Earth, Elite, Epic, Fallen, Fierce, Fire, Furious, Gamma, Glowing, Green, Heroic, Howling, Laser, Lethal, Light, Lightning, Metal, Mighty, Neon, Omega, Orange, Pale, Pink, Purple, Quick, Radiant, Red, Rock, Shadow, Stealth, Super, Thunder, Twilight, Velvet, Void, Water, Wild, Wind, Wood, Yellow
- 2e mot : Beast, Blast, Brawler, Cobra, Colonel, Commander, Commando, Defender, Dragon, Eagle, Falcon, Force, General, Guardian, Gunner, Gunslinger, Hero, King, Knight, Leader, Lion, Mantis, Marshal, Master, Monkey, Moon, Ninja, Officer, Operative, Pirate, Queen, Raptor, Samurai, Scorpion, Sheriff, Snake, Sniper, Soldier, Star, Storm, Sun, T-Rex, Tiger, Vampire, Villain, Viper, Warrior, Werewolf, Wolf, Zombie

Exemples : `09 32` = Crimson Zombie, `07 1c` = Cool Officer. Les traductions ne sont pas dans l'APK (noms affichés en anglais).
