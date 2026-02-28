[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# digiOBSCURA - Un capteur d'image et un appareil photo numérique DIY 📷

[![License: CC BY-NC-SA 3.0 (firmware header)](https://img.shields.io/badge/Firmware%20Header-CC%20BY--NC--SA%203.0-lightgrey?style=for-the-badge)](https://creativecommons.org/licenses/by-nc-sa/3.0/)
[![Repo License](https://img.shields.io/badge/Repository%20License-GPL--3.0-2563eb?style=for-the-badge)](./LICENSE)
[![Project Type](https://img.shields.io/badge/Type-DIY%20Hardware%20%2B%20Firmware-f97316?style=for-the-badge)](#overview)
[![Sensor](https://img.shields.io/badge/Sensor-32x32%20(1024px)-16a34a?style=for-the-badge)](#features)
[![MCU](https://img.shields.io/badge/MCU-SAMD21G18A-1d4ed8?style=for-the-badge)](#hardware-firmware-architecture-current)
[![Contributions](https://img.shields.io/badge/Contributions-Welcome-0f766e?style=for-the-badge&logo=github)](#contributing)
[![GitHub Stars](https://img.shields.io/github/stars/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/lachlanchen/CustomSensor?style=for-the-badge)](https://github.com/lachlanchen/CustomSensor/issues)

> Une pile d'imagerie DIY entièrement ouverte : PCB capteur personnalisé, carte MCU, firmware, visionneuse webcam et pièces d'enceinte imprimables.

## 📚 Table des matières

- [Aperçu](#aperçu)
- [Démo](#démo)
- [Fonctionnalités](#fonctionnalités)
- [Structure du projet](#structure-du-projet)
- [Architecture Matérielle/Firmware (actuelle)](#architecture-matériellefirmware-actuelle)
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Utilisation](#utilisation)
- [Notes de configuration](#notes-de-configuration)
- [Exemples](#exemples)
- [Notes de développement](#notes-de-développement)
- [Dépannage](#dépannage)
- [Feuille de route et notes techniques](#feuille-de-route-et-notes-techniques)
- [Contribuer](#contribuer)
- [Support](#%E2%9D%A4%EF%B8%8F-support)
- [Licence](#licence)

## Aperçu 🛰️

`digiOBSCURA` est un système d'imagerie 32x32 DIY construit autour d'une matrice de phototransistors personnalisée et d'un contrôleur basé sur SAMD21.

Ce dépôt contient :
- Un firmware pour la capture autonome vers carte SD.
- Un firmware + un sketch Processing pour la visualisation en direct de type webcam.
- Les sources complètes KiCad pour les deux cartes (`image_sensor` et `mcu`) ainsi que les Gerbers de production.
- Les actifs mécaniques (`.f3d`, `.stl`) pour l'enceinte, la lentille et les pièces de trépied.
- Des notes de conception et une feuille de route d'améliorations progressives dans [`docs/`](./docs/).

## Fiche rapide 📌

| Élément | Détails |
| --- | --- |
| Taille du projet | Appareil photo + firmware + visionneuse + design mécanique |
| Matrice capteur | `32x32` (`1024`) matrice de phototransistors personnalisée |
| Contrôleur principal | `SAMD21G18A` |
| Sortie d'image | BMP sur carte SD / flux de trame série en direct |
| Flux de travail visés | Capture autonome • Prévisualisation de type webcam |

## Démo 🎬

Voir la vidéo :

[![Vidéo digiOBSCURA](https://img.youtube.com/vi/PaXweP73NT4/0.jpg)](https://youtu.be/PaXweP73NT4)

Trouvez la nomenclature (BOM) des deux cartes ici : https://www.findchips.com/org/10-open-hardware/list/162969-digiobscura-diy-image-sensor-and-digital-camera

## Fonctionnalités ✨

- Matrice d'imagerie personnalisée de phototransistors 32x32 (1024 pixels).
- Adressement double par ADG732 pour la sélection des pixels ligne/colonne.
- Workflow caméra autonome :
  - Interface utilisateur OLED.
  - Entrée déclencheur.
  - Sortie SD (`DGO_####.bmp`, auto-incrémentés).
- Workflow webcam :
  - Le firmware Arduino répond aux requêtes de pixels série à `115200`.
  - L'application Processing affiche un aperçu en niveaux de gris 32x32 en direct.
- Source matérielle ouverte :
  - Schémas/PCBs KiCad.
  - Sorties de production et artefacts BOM.
  - Fichiers mécaniques imprimables.

## Structure du projet 🧱

```text
CustomSensor/
|- README.md
|- LICENSE
|- Firmware/
|  |- digiOBSCURA_camera/
|  |  |- digiOBSCURA_camera.ino
|  |- digiOBSCURA_Webcam_Arduino/
|  |  |- digiOBSCURA_Webcam_Arduino.ino
|  `- digiOBSCURA_Webcam_Processing/
|     `- digiOBSCURA_Webcam_Processing.pde
|- docs/
|  |- overview.md
|  |- 01-firmware-differencing.md
|  |- 02-comparator-event-mode.md
|  |- 03-bias-and-buffer-upgrade.md
|  `- 04-full-event-sensor-redesign.md
|- PCB/
|  |- image_sensor/
|  |  |- digiobscura.kicad_pcb
|  |  |- digiobscura.sch
|  |  |- sym-lib-table
|  |  `- fp-lib-table
|  `- mcu/
|     |- digiobscura_mcu.kicad_pcb
|     |- digiobscura_mcu.sch
|     |- sym-lib-table
|     `- fp-lib-table
|- PCB-Production/
|- F3D/
|- STL/
|- logo/
|- i18n/
|- .auto-readme-work/
|  |- pipeline-context.md
|  `- repo-structure-analysis.md
`- .git
```

## Architecture Matérielle/Firmware (actuelle) 🧰

Basé sur [`docs/overview.md`](./docs/overview.md) :

| Sous-système | Détails |
|---|---|
| Matrice capteur | `1024 x ALS-PT19` phototransistors |
| Addressage | `2 x ADG732` multiplexeurs analogiques (ligne + colonne) |
| Carte principale | `SAMD21G18A` |
| Chemin d'alimentation | Batterie `18650`, chargeur `MCP73831`, régulation `3.3 V` |
| Chemin signal | `OUTCOL` est forcé au niveau haut par GPIO du MCU ; le chemin capteur sélectionné est lu sur `OUTROW` via l'ADC |
| Comportement de capture | Le firmware caméra effectue des balayages pleine image et écrit des fichiers BMP 24 bits |

## Prérequis 🧰

### Matériel

- Cartes `image_sensor` et `mcu` assemblées (ou câblage équivalent).
- Carte cible compatible SAMD21 avec un brochage correspondant à celui du firmware.
- Carte MicroSD (mode caméra).
- OLED `64x48` SSD1306 (mode caméra, SPI logiciel 4 broches dans le sketch actuel).
- Entrée bouton/interrupteur de déclenchement.

### Logiciel

- Arduino IDE (ou Arduino CLI) avec un paquet de cartes SAMD21.
- Bibliothèques Arduino requises pour le firmware caméra :
  - `U8g2`
  - `SPI`
  - `SD`
- IDE/runtime Processing pour [`Firmware/digiOBSCURA_Webcam_Processing`](./Firmware/digiOBSCURA_Webcam_Processing/).

## Installation 🧩

1. Clonez ce dépôt :

```bash

git clone https://github.com/lachlanchen/CustomSensor.git
cd CustomSensor
```

2. Ouvrez un sketch firmware dans Arduino IDE :
- Mode caméra : [`Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino`](./Firmware/digiOBSCURA_camera/digiOBSCURA_camera.ino)
- Mode webcam : [`Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino`](./Firmware/digiOBSCURA_Webcam_Arduino/digiOBSCURA_Webcam_Arduino.ino)

3. Sélectionnez la carte SAMD21 et le port corrects, puis téléversez.

4. En mode webcam, ouvrez le sketch Processing :
- [`Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde`](./Firmware/digiOBSCURA_Webcam_Processing/digiOBSCURA_Webcam_Processing.pde)

## Utilisation 🚀

### Mode caméra (flux de capture SD) 📸

1. Flashez `digiOBSCURA_camera.ino`.
2. Insérez une carte SD formatée en FAT/FAT32.
3. Alimentez/réinitialisez la carte ; le firmware cherche le prochain nom libre `DGO_####.bmp`.
4. Lancez la capture avec l'entrée déclencheur.
5. L'appareil capture et écrit un BMP sur SD puis affiche une miniature sur l'écran OLED.

### Mode webcam (aperçu en direct rendu par l'hôte) 🖥️

1. Flashez `digiOBSCURA_Webcam_Arduino.ino`.
2. Connectez en série USB à `115200`.
3. Dans le sketch Processing, réglez `String portName` sur votre port série réel (le modèle utilise `COM32`).
4. Lancez le sketch Processing ; il interroge 32x32 pixels et affiche une trame en niveaux de gris en direct.

## Notes de configuration 🛠️

Réglages par défaut actuels au niveau firmware :

| Paramètre | Valeur/comportement actuel |
|---|---|
| Résolution | `32x32` fixe |
| Résolution ADC | `analogReadResolution(12)` |
| Moyennage en mode caméra | `avgCount = 10` (meilleure qualité, cadence d'image plus basse) |
| Broche CS SD (sketch caméra) | `cardPin = 21` |
| Baud série (chemin webcam) | `115200` |
| Cartographie E/S | Tableaux/variables en dur (`SCOL`, `SROW`, `WR`, `CS`, `EN`, `OUTCOL`, `OUTROW`, etc.) |

Si votre câblage diffère, mettez à jour les définitions de broches en haut de chaque fichier `.ino` avant le flash.

## Exemples 🧪

### Ouvrir et modifier le firmware caméra

```bash
cd Firmware/digiOBSCURA_camera
# open digiOBSCURA_camera.ino in Arduino IDE
```

### Ouvrir et modifier la visionneuse webcam Processing

```bash
cd Firmware/digiOBSCURA_Webcam_Processing
# open digiOBSCURA_Webcam_Processing.pde in Processing IDE
```

### Ouvrir les sources PCB dans KiCad

- `PCB/image_sensor/digiobscura.sch`
- `PCB/image_sensor/digiobscura.kicad_pcb`
- `PCB/mcu/digiobscura_mcu.sch`
- `PCB/mcu/digiobscura_mcu.kicad_pcb`

## Notes de développement 📘

- Aucun système de build unifié (`Makefile`, `platformio.ini`, `package.json`, etc.) n'est présent dans ce dépôt.
- Le développement couvre actuellement Arduino + Processing + KiCad + fichiers CAO mécanique.
- `PCB/image_sensor/README.md` et `PCB/mcu/README.md` contiennent actuellement du contenu de type placeholder/template et ne sont pas des références définitives.
- `i18n/` contient des variantes README traduites ; elles peuvent être incomplètes par endroits et en retard par rapport au contenu anglais.

## Dépannage 🧯

| Symptôme | Vérifications |
|---|---|
| Carte SD non détectée en mode caméra | Vérifiez le câblage et `cardPin` (`21` dans le sketch actuel). Reformatez la carte SD (FAT/FAT32) et réessayez. |
| Données d'image vides/bruitées | Vérifiez le câblage `OUTCOL`/`OUTROW` et les broches de contrôle du multiplexeur. Vérifiez l'intégrité de l'alimentation et les connexions de la carte capteur. |
| La visionneuse Processing n'affiche pas de données | Vérifiez le baud `115200` côté Arduino et Processing. Mettez `portName` de `COM32` vers votre port réel. |
| Capture très lente | C'est normal avec un scan 32x32 complet et un moyennage (`avgCount=10` par défaut). |

## Feuille de route et notes techniques 🗺️

Le dossier [`docs/`](./docs/) suit les axes techniques prévus et exploratoires :

| Document | Sujet |
|---|---|
| [`docs/01-firmware-differencing.md`](./docs/01-firmware-differencing.md) | Différenciation temporelle firmware / événements |
| [`docs/02-comparator-event-mode.md`](./docs/02-comparator-event-mode.md) | Détection d'événements assistée par comparateur |
| [`docs/03-bias-and-buffer-upgrade.md`](./docs/03-bias-and-buffer-upgrade.md) | Améliorations front-end analogique et du biais |
| [`docs/04-full-event-sensor-redesign.md`](./docs/04-full-event-sensor-redesign.md) | Options de refonte d'un capteur événementiel à plus long terme |

## Contribuer 🤝

Les contributions sont les bienvenues. Domaines suggérés :

- Notes de mise en service matériel et cartographies de broches vérifiées.
- Validation de la chaîne d'outils (cœur Arduino, bibliothèques, version de Processing).
- Mises à jour de documentation, notamment les schémas de câblage et la calibration.

- README i18n traduits une fois les langues cibles finalisées.

Pour les changements touchant le matériel ou le comportement firmware, incluez :
- Détails du montage de test.
- Contexte de révision de la carte.
- Observations de comportement avant/après.

## Licence

- Le fichier `LICENSE` de ce dépôt indique actuellement une distribution GPL-3.0 au niveau du dépôt.
- Les en-têtes du firmware mentionnent également `CC BY-NC-SA 3.0` pour une partie du contenu.

En redistribuant des binaires, artefacts matériels ou dérivés, vérifiez les conditions de licence exactes dans les fichiers concernés et assurez la compatibilité avec votre usage.


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
