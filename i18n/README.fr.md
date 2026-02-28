[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# digiOBSCURA - Un capteur d'image et appareil photo numérique DIY 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-blue)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-orange)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-2ea44f)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1f6feb)](#hardwarefirmware-architecture-current)

> Une pile d'imagerie DIY entièrement ouverte : PCB de capteur personnalisé, carte MCU, firmware, visualiseur webcam et pièces de boîtier imprimables.

## Présentation
`digiOBSCURA` est un système d'imagerie DIY 32x32 construit autour d'une matrice personnalisée de phototransistors et d'un contrôleur basé sur SAMD21.

Ce dépôt contient :
- Firmware pour la capture autonome vers carte SD.
- Firmware + sketch Processing pour l'affichage en direct type webcam via série.
- Fichiers source KiCad complets pour les deux cartes (`image_sensor` et `mcu`) ainsi que les Gerbers de production.
- Ressources mécaniques (`.f3d`, `.stl`) pour les composants de boîtier/objectif/trépied.
- Notes de conception et documents de feuille de route des mises à niveau par étapes dans [`docs/`](./docs/).

### En bref
| Domaine | Implémentation actuelle |
|---|---|
| Matrice d'imagerie | Matrice personnalisée de phototransistors `32x32` (`1024` pixels) |
| Multiplexage | `2 x ADG732` (sélection ligne + colonne) |
| Contrôleur | `SAMD21G18A` |
| Sortie caméra | Fichiers BMP sur carte SD (`DGO_0000.bmp`, auto-incrémenté) |
| Mode live | Série (`115200`) + rendu hôte Processing |
| Ressources de conception | KiCad + Gerbers + artefacts BOM + fichiers `F3D`/`STL` |

## Démo (contenu canonique existant) 🎬
Regardez la vidéo :  
[![digiOBSCURA Video](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Retrouvez la BOM des deux cartes ici : https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Fonctionnalités
- Matrice d'imagerie personnalisée de phototransistors 32x32 (1024 pixels).
- Adressage double mux ADG732 pour la sélection de pixels ligne/colonne.
- Flux de travail caméra autonome :
  - Interface OLED.
  - Entrée déclencheur.
  - Sortie carte SD (`DGO_0000.bmp`, auto-incrémenté).
- Flux de travail webcam :
  - Le firmware Arduino répond aux requêtes de pixels série à `115200`.
  - L'application Processing affiche une vue 32x32 en niveaux de gris en direct.
- Matériel open source :
  - Schémas/PCB KiCad.
  - Sorties de production et artefacts BOM.
  - Fichiers mécaniques imprimables.

## Structure du projet

```text
CustomSensor/
|- README.md
|- LICENSE
|- Firmware/
|  |- digiOBSCURA_camera/digiOBSCURA_camera.ino
|  |- digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino
|  `- digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde
|- docs/
|  |- overview.md
|  |- 01-firmware-differencing.md
|  |- 02-comparator-event-mode.md
|  |- 03-bias-and-buffer-upgrade.md
|  `- 04-full-event-sensor-redesign.md
|- PCB/
|  |- image_sensor/
|  `- mcu/
|- PCB-Production/
|- F3D/
|- STL/
|- logo/
`- i18n/
```

## Architecture matériel/firmware (actuelle)
Basée sur [`docs/overview.md`](./docs/overview.md) :

| Sous-système | Détails |
|---|---|
| Matrice capteur | `1024 x ALS-PT19` phototransistors |
| Adressage | `2 x ADG732` mux analogiques (ligne + colonne) |
| Carte cœur | `SAMD21G18A` |
| Chaîne d'alimentation | Batterie `18650`, chargeur `MCP73831`, régulation `3.3 V` |
| Chaîne de signal | `OUTCOL` est forcé à l'état haut par un GPIO MCU ; le chemin capteur sélectionné est lu sur `OUTROW` via ADC |
| Comportement de capture | Le firmware caméra effectue des scans full-frame et écrit des fichiers BMP 24 bits |

## Prérequis

### Matériel
- Cartes `image_sensor` et `mcu` assemblées (ou câblage équivalent).
- Carte cible compatible SAMD21 correspondant aux affectations de broches utilisées dans le firmware.
- Carte MicroSD (mode caméra).
- OLED SSD1306 64x48 (mode caméra, SPI logiciel 4 fils dans le sketch actuel).
- Bouton/interrupteur de déclenchement.

### Logiciel
- Arduino IDE (ou Arduino CLI) avec un package de carte SAMD21.
- Bibliothèques Arduino requises pour le firmware caméra :
  - `U8g2`
  - `SPI`
  - `SD`
- IDE/runtime Processing pour [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## Installation

1. Clonez ce dépôt :

```bash
git clone https://github.com/<your-org-or-user>/CustomSensor.git
cd CustomSensor
```

2. Ouvrez un sketch firmware dans Arduino IDE :
- Mode caméra : [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Mode webcam : [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Sélectionnez la carte/port SAMD21 correct puis téléversez.

4. Pour le mode webcam, ouvrez le sketch Processing :
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Utilisation

### Mode caméra (flux de capture SD) 📸
1. Flashez `digiOBSCURA_camera.ino`.
2. Insérez la carte SD.
3. Au démarrage, le firmware vérifie les noms de fichiers et trouve le prochain `DGO_####.bmp` libre.
4. Appuyez sur l'entrée déclencheur.
5. L'appareil capture, écrit le BMP sur la SD et affiche un aperçu miniature sur l'OLED.

### Mode webcam (vue live rendue côté hôte) 🖥️
1. Flashez `digiOBSCURA_Webcam_Arduino.ino`.
2. Connectez-vous en USB série (`115200`).
3. Dans le sketch Processing, définissez le bon port série (le fichier contient actuellement `String portName = "COM32"`).
4. Exécutez le sketch Processing pour interroger les valeurs de pixels et dessiner la trame 32x32.

## Notes de configuration
Valeurs par défaut actuelles au niveau firmware, d'après le code source :

| Paramètre | Valeur/comportement actuel |
|---|---|
| Résolution | `32x32` fixe |
| Résolution ADC | `analogReadResolution(12)` |
| Moyennage en mode caméra | `avgCount = 10` (augmente la qualité, réduit la vitesse de trame) |
| Broche CS SD (sketch caméra) | `cardPin = 21` |
| Débit série (chemin webcam) | `115200` |
| Mapping E/S | Tableaux/variables codés en dur (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, etc.) |

Si votre câblage matériel diffère, mettez à jour les définitions de broches en haut de chaque fichier `.ino` avant le flash.

## Exemples

### Ouvrir et modifier le firmware caméra
```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Ouvrir et modifier le visualiseur webcam Processing
```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### Ouvrir les sources PCB dans KiCad
- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Notes de développement
- Il n'existe pas de système de build unifié (`Makefile`, `platformio.ini`, `package.json`, etc.) dans ce dépôt.
- Le développement couvre actuellement Arduino + Processing + KiCad + fichiers de CAO mécanique.
- `PCB/image_sensor/README.md` et `PCB/mcu/README.md` semblent obsolètes/de type template et ne sont pas actuellement des documents de référence faisant autorité.
- `i18n/` existe et est actuellement vide ; les traductions sont prévues mais pas encore validées dans un commit.

## Documentation et feuille de route
Le répertoire `docs/` contient des notes d'évolution technique par étapes :

| Document | Focus |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | Différenciation temporelle/événements uniquement firmware |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | Détection d'événements assistée par comparateur |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | Améliorations du front-end analogique/bias |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | Options de refonte de capteur événementiel à plus long terme |

## Dépannage
| Symptôme | Vérifications |
|---|---|
| Carte SD non détectée en mode caméra | Confirmez le câblage et `cardPin` (`21` dans le sketch actuel). Reformatez la carte SD (FAT/FAT32) et réessayez. |
| Données d'image vides/bruitées | Vérifiez le câblage `OUTCOL`/`OUTROW` et les broches de contrôle des mux. Vérifiez l'intégrité de l'alimentation et les connexions de la carte capteur. |
| Le visualiseur Processing n'affiche aucune donnée | Confirmez le débit série `115200` dans Arduino et Processing. Remplacez `portName` de `COM32` par votre port réel. |
| Capture très lente | Attendu avec un scan 32x32 complet et le moyennage (`avgCount=10` par défaut). |

## Support / Sponsor
D'après les en-têtes du firmware :

- Créateur : Sean Hodgins
- YouTube : http://youtube.com/seanhodginsfilms
- Patreon : https://patreon.com/seanhodgins

Si vous vous appuyez sur ce projet, envisagez de soutenir le créateur d'origine.

## Contribution
Les contributions sont les bienvenues. Domaines de contribution suggérés :

- Notes de mise en route matériel et mappings de broches vérifiés.
- Validation toolchain/version (Arduino core, bibliothèques, version de Processing).
- Mises à jour de documentation, surtout schémas de câblage et workflow de calibration.
- Traductions README dans `i18n/` une fois les langues cibles finalisées.

Pour les changements touchant le comportement matériel ou firmware, incluez :
- Détails de la configuration de test.
- Contexte de révision de carte.
- Notes de comportement avant/après.

## Licence
- Fichier de licence du dépôt : [`LICENSE`](./LICENSE) (texte GPL-3.0 présent dans le dépôt).
- Note d'en-tête firmware : les en-têtes `.ino` actuels indiquent `cc-by-sa-nc-3.0`.

Note importante : ces deux signaux de licence sont différents. Jusqu'à clarification par les mainteneurs, traitez la licence comme nécessitant une confirmation du propriétaire du projet pour la redistribution/l'usage commercial.

## Remerciements
- Sean Hodgins / travaux du projet digiOBSCURA et documentation/vidéo publique.
- Ressources de la communauté open hardware et maker qui rendent possibles les expérimentations de capteurs DIY.
