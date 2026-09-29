# TED Energy Pilot

TED pilote vos onduleurs et batteries solaires à partir des mesures de votre
compteur d'électricité : il charge les batteries avec le surplus solaire et
les décharge quand la maison consomme, pour acheter le moins possible au réseau.

Guide complet, avec captures et dépannage :
<https://ted-energy.fr/installation/home-assistant>

## Avant de commencer

- Vos onduleurs et batteries sont visibles dans Home Assistant (intégration de
  leur marque).
- Votre compteur d'électricité remonte dans Home Assistant (mesure positive en
  import, négative en injection), ou un compteur Shelly est lisible sur le réseau.
- C'est tout : la licence TED, gratuite, s'obtient depuis TED au premier
  lancement (prénom et email).

## Première mise en route

1. Onglet **Info** : activez **Démarrage au boot**, **Chien de garde** et
   **Afficher dans la barre latérale**, puis cliquez sur **Démarrer**.
2. Ouvrez TED : entrée **TED** de la barre latérale, ou bouton
   **Ouvrir l'interface web**.
3. L'assistant de démarrage vous propose la licence gratuite : indiquez votre
   prénom et votre email, TED l'obtient auprès de ted-energy.fr et l'active
   tout seul. Vous avez déjà un fichier de licence ? Page **Paramètres** →
   **Sauvegarde, restauration et licence** → **Importer une licence…**.
4. Suivez la mise en route (compteur, puis onduleurs) :
   <https://ted-energy.fr/mise-en-route>

## Connexion à Home Assistant

Automatique : TED utilise l'accès fourni par Home Assistant. Il n'y a ni adresse
ni jeton d'accès à saisir (ces réglages sont masqués dans les Paramètres de TED).

## Options

- **Journal détaillé** : affiche chaque cycle de régulation dans l'onglet
  **Journal**. Utile pour un diagnostic, à désactiver ensuite.
- **Conservation des journaux (jours)** : TED écrit 20 à 30 Mo de journaux par
  jour ; au-delà de ce nombre de jours, les plus anciens sont supprimés.

## Réseau et application mobile

La rubrique **Réseau** de l'onglet **Configuration** publie l'interface de TED
sur un port de la machine Home Assistant (**5001** par défaut) :
`http://<adresse de Home Assistant>:5001`. C'est l'adresse à utiliser pour
l'application Android TED et pour l'accès à distance (voir dans TED :
Paramètres → Application mobile et accès à distance).

Si vous videz ce champ, TED reste accessible depuis la barre latérale de Home
Assistant, mais plus depuis le téléphone.

## Vos données

Réglages, onduleurs, historique, licence et journaux sont enregistrés dans le
dossier `local_ted` (installation manuelle) ou `<identifiant>_ted` (installation
depuis le dépôt TED) du partage Samba `app_configs` (anciennement
`addon_configs`). Ils sont conservés lors des mises à jour et inclus dans les
sauvegardes de Home Assistant, à l'exception des journaux.

### Venir d'une installation Docker de TED

1. Dans l'ancienne installation, arrêtez TED.
2. Ici, arrêtez l'application TED.
3. Copiez le contenu de l'ancien dossier `runtime` dans le dossier
   `local_ted/runtime` du partage `app_configs`.
4. Démarrez l'application TED : vous retrouvez vos réglages et votre licence.

N'utilisez pas les deux installations en même temps : elles piloteraient les
mêmes onduleurs.

## Mise à jour

**Installé depuis le dépôt TED** (Boutique → ⋮ → Dépôts) : Home Assistant
signale lui-même chaque nouvelle version de TED ; cliquez sur **Mettre à jour**
(ou activez **Mise à jour automatique** dans l'onglet Info).

**Installé à la main** (applications locales) :

1. Téléchargez le nouveau paquet sur <https://ted-energy.fr/telecharger>
   (choix « Home Assistant »).
2. Remplacez le dossier `ted` des applications locales (partage `local_apps`,
   anciennement `addons`) par celui du paquet : même méthode qu'à
   l'installation, la commande du Terminal le fait d'elle-même.
3. Paramètres → Applications → Boutique → menu ⋮ → **Rechercher des mises à
   jour**, puis **Mettre à jour** sur TED.

### Passer de l'installation manuelle au dépôt TED

Les deux installations n'ont pas le même dossier de données (`local_ted` pour
l'installation manuelle, `<identifiant>_ted` pour le dépôt) :

1. Ajoutez le dépôt TED et installez TED depuis la Boutique, sans le démarrer.
2. Arrêtez l'ancienne application TED (applications locales).
3. Copiez `local_ted/runtime` dans le dossier `…_ted/runtime` du partage
   `app_configs`.
4. Démarrez la nouvelle application, vérifiez vos réglages, puis désinstallez
   l'ancienne.

## Dépannage

- **Onglet Journal** : messages de démarrage de TED et erreurs.
- **L'installation échoue** : la machine doit avoir accès à Internet pendant
  l'installation (téléchargement de l'image de TED depuis Docker Hub).
- **Le compteur ou les onduleurs ne remontent pas** : dans TED, Paramètres →
  Connexion Home Assistant et compteur → **Tester**, puis vérifiez les noms
  d'entités (ils doivent exister dans Outils de développement → États).
- **Le port 5001 est déjà utilisé** : choisissez un autre port dans
  Configuration → Réseau (entre 5000 et 5009 pour que l'application mobile le
  trouve toute seule), puis redémarrez TED.

Aide et communauté : <https://discord.gg/BNy9QkPMkZ>
