# Journal des versions

Nouveautés détaillées de chaque version : <https://ted-energy.fr/evolutions>

## 3.6

- Nouveau : réseau triphasé. L'assistant de démarrage demande d'abord
  monophasé ou triphasé ; en triphasé, TED lit les trois phases (trois entités
  Home Assistant ou un Shelly Pro 3EM, profil triphasé ou monophasé) et propose
  les abonnements de 9 à 36 kVA (3 à 12 kVA en monophasé). Bascule à tout
  moment dans Paramètres › Réseau électrique.
- Deux régulations triphasées : « Somme des phases » (par défaut, compteur
  Linky qui additionne les phases : un onduleur de la phase 1 couvre aussi la
  consommation des phases 2 et 3) ou « Phase par phase » (une régulation par
  phase, pour un compteur qui compte chaque phase ; une phase peut charger sur
  son surplus pendant qu'une autre décharge, mode Assisté possible phase par
  phase).
- Garde-fous par phase (charge et décharge maximales) et délestage de la
  charge planifiée phase par phase.
- Chaque onduleur (hybride ou solaire) indique sa phase ; « Identifier la
  phase » la trouve automatiquement (test de 30 s). Mode Assisté décoché par
  défaut au passage en triphasé.
- Tableau de bord : une liaison par phase entre le réseau et la maison, une
  carte par phase en régulation phase par phase, onduleurs regroupés par phase,
  trois courbes Réseau L1 / L2 / L3 dans l'historique. Variables
  d'automatisation par phase (grid.l1.power_w, phase.1.mode…).
- Nouveau : automatisations de fonctionnement (page Automatisations, bloc
  « Fonctionnement de la régulation »). Tant qu'une condition est vraie, une règle
  applique un ou plusieurs effets : à toute la régulation (veille forcée, décharge ou charge
  bloquée, charge forcée, puissance totale limitée) ou à un onduleur, un
  cluster, une zone électrique ou tous les onduleurs (activé, désactivé,
  charge ou décharge bloquée, puissance limitée). Tout reprend comme avant dès
  que la condition redevient fausse. Modèle mis en avant : heures creuses et
  batterie sous 50 % → veille (la batterie se garde pour les heures pleines).
- Conditions des automatisations et notifications : heures creuses en cours,
  période tarifaire, prix du kWh, couleurs Tempo, niveau des batteries des
  onduleurs pilotés, et n'importe quelle entité de Home Assistant.
- Recherche des entités de Home Assistant partout où il fallait les saisir :
  conditions, actions, onduleurs, paramètres. Remplissage automatique des
  entités d'un onduleur hybride Zendure à partir de son appareil Home Assistant.
- Nouveau : onduleurs solaires sans batterie (micro-onduleurs Hoymiles via
  OpenDTU ou toute autre marque). Une entité Home Assistant par entrée (MPPT),
  au moins une obligatoire : la production s'affiche sur le tableau de bord,
  compte dans les statistiques de production solaire et peut servir dans les
  automatisations. TED ne leur envoie jamais de consigne.
- Page Onduleurs : « Ajouter un onduleur hybride » (onduleurs à batterie) et
  « Ajouter un onduleur solaire » (panneaux solaires seuls).
- Page Onduleurs : les onduleurs se réordonnent par glisser-déposer, à
  l'intérieur de leur bloc (un onduleur ne quitte jamais son cluster ou sa
  zone). Ordre purement visuel, repris par le tableau de bord.
- Mode Autonome : un seul objectif au compteur (0 W par défaut) remplace les
  trois plages fixes Hiver / Standard / Été ; une installation existante garde
  l'objectif de la plage qui était active.
- Nouveau : objectif différent selon la période de l'année (désactivé par
  défaut). Vous découpez l'année en plages (deux au minimum, bouton « Ajouter
  une plage ») avec un nom, une date de début et un objectif ; TED change de
  plage tout seul à la date prévue et vous prévient par une notification. Lien
  vers l'outil PVGIS pour choisir les dates selon la production solaire chez
  vous.
- Tableau de bord allégé : le flux d'énergie en direct remplace les quatre
  indicateurs du haut ; un petit anneau du bloc Maison montre en direct la part
  de la consommation couverte sans le réseau. Un clic sur un jour du graphique affiche son bilan
  (flèches pour passer d'un jour à l'autre), sur 7 ou 30 jours.
- Textes de l'interface plus directs et plus clairs.
- L'application s'appelle désormais « TED Energy Controller », comme
  l'interface.

## 3.5

- Licences FREE (gratuite, sans limite de durée, 2 onduleurs pilotés), ACCESS
  (découverte sans limite, puis FREE) et FULL POWER ; demande de prolongation
  et transfert de la licence sur une nouvelle machine depuis TED.
- Avertissement de sécurité (installation électrique, NF C 15-100) à accepter
  pour chaque nouvelle licence.
- Nouvelle version de TED signalée dans l'interface (tableau de bord, barre du
  haut, page Aide, notification) et dans l'application mobile, avec la marche à
  suivre. Vérifiée en même temps que la licence, sans connexion supplémentaire.

## 3.4

- Installation depuis le dépôt TED : mises à jour signalées par Home
  Assistant. Programme compilé, identique pour Intel/AMD et ARM64.
- Licence gratuite obtenue directement depuis TED (assistant de démarrage ou
  Paramètres › Licence) : prénom et email, activation automatique.
- Abonnement et tarifs : jusqu'à 6 prix (Tempo, heures super creuses,
  périodes personnalisées), horaires automatiques ou peints à la main. Le
  bilan du jour et des 7 derniers jours détaille le réseau par période et
  s'affiche en kWh ou en euros.
- Charge planifiée : une seule source au choix, calendrier manuel ou
  périodes tarifaires cochées.
- Rapports PDF quotidiens, hebdomadaires ou mensuels (page Automatisations).
- Assistant de démarrage à la première utilisation.
- Erreur 01 : l'onduleur bloqué est testé régulièrement (toutes les 10 min par
  défaut) avec une petite décharge, et débloqué s'il la produit.
- Nouvelle installation : réglages neutres (charge planifiée désactivée,
  abonnement et compteur à renseigner).

## Première version en application Home Assistant

- TED s'installe directement dans Home Assistant OS, sans Docker.
- Interface dans la barre latérale de Home Assistant.
- Connexion à Home Assistant automatique, sans jeton d'accès.
- Données dans le dossier `local_ted` du partage `app_configs`, incluses dans
  les sauvegardes de Home Assistant (hors journaux, purgés automatiquement).
