# TED Energy Controller — dépôt Home Assistant

TED pilote vos onduleurs et batteries solaires depuis Home Assistant pour
consommer votre propre production (charge sur le surplus, décharge quand la
maison consomme, heures creuses, multimarque).

Site, documentation et licence gratuite : <https://ted-energy.fr>

## Installer TED dans Home Assistant OS

[![Ajouter le dépôt TED à Home Assistant](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https://github.com/LaurentFoulon/ted-home-assistant)

Ou à la main :

1. **Paramètres → Applications → Boutique** → menu **⋮** → **Dépôts**.
2. Ajoutez l'adresse : `https://github.com/LaurentFoulon/ted-home-assistant`
3. Installez **TED Energy Controller**, puis suivez l'onglet **Documentation**.

Home Assistant signale ensuite automatiquement chaque nouvelle version de TED.

## Docker (NAS, mini-PC, Raspberry Pi…)

La même image est publiée sur Docker Hub pour Intel/AMD et ARM64 :

```bash
docker pull tedenergy/ted:latest
```

Guide d'installation : <https://ted-energy.fr/installation>

## Contenu de ce dépôt

Ce dépôt ne contient que la description de l'application pour Home Assistant
(`ted/config.yaml`, documentation, icônes, traductions). Le programme TED est
distribué sous forme d'image Docker compilée (`tedenergy/ted`), sans code source.
TED est un logiciel propriétaire : tous droits réservés.

## Aide

- Communauté Discord : <https://discord.gg/BNy9QkPMkZ>
- Nouveautés : <https://ted-energy.fr/evolutions>
