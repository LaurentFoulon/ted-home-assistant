# Journal des versions

Nouveautés détaillées de chaque version : <https://ted-energy.fr/evolutions>

## 3.7

- Automatisations réunies : la page Automatisations n'a plus qu'un seul
  tableau (les anciennes « Actions Home Assistant » et « Fonctionnement de
  la régulation » y sont reprises automatiquement, dans cet ordre). Une même
  automatisation peut, tant que sa condition est vraie, imposer des effets
  (veille, charge forcée, charge ou décharge bloquée ou limitée, onduleur
  activé ou désactivé) et désormais aussi : un SOC minimum ou maximum à un
  ou plusieurs onduleurs (à la place de leurs réglages ; Zendure : écrit
  aussi dans ses entités min_soc / soc_set), n'importe quel réglage de la
  régulation de la page Paramètres (objectif au compteur, puissances,
  seuils…), une valeur à une entité de Home Assistant (nombre, liste de
  choix, texte, marche / arrêt, date). Rien n'est enregistré dans la
  configuration : tout revient comme avant à la fin, une entité de Home
  Assistant retrouve sa valeur d'origine (même après un redémarrage). Elle
  peut aussi lancer des actions à son début et à sa fin, dont deux
  nouvelles : régler une entité, appeler n'importe quel service de Home
  Assistant (avec ses données). Délai minimal entre deux débuts, ordre de
  priorité (flèches ↑ ↓), réglage imposé signalé dans la page Paramètres.
  Le modèle « Auto-calibration » désactive l'onduleur et passe son SOC
  maximum à 100 % ; nouveau modèle « La nuit : garder de la réserve ».
- Bilan énergétique : l'énergie injectée sur le réseau est comptée par
  période tarifaire (colonne « Injecté » du bilan en euros, détail sous
  « Injecté sur le réseau », total des 7 / 30 derniers jours). Nouveau
  réglage « Prix de revente du kWh injecté » (Paramètres › Abonnement et
  tarifs, valable même sans abonnement) : le bilan du jour, celui des 7 /
  30 jours et les rapports PDF affichent alors la revente du surplus et la
  facture nette (coût du réseau − revente).
- Rapports PDF : première page refaite (plus aucun texte superposé ni
  coupé) : 10 indicateurs sur deux lignes (dont injecté, revente, facture
  nette et gain de l'installation), un seul tableau des origines pour les
  deux anneaux, injection et revente sous l'axe des graphiques jour par
  jour, nouveau tableau « Bilan financier », colonnes « Injecté » et
  « Revente » dans les détails.
- Mode Assisté : nouveau réglage « Durée de maintien de la consigne »
  (20 s par défaut, jusqu'à 60 s). La consigne des onduleurs pilotés ne
  change plus qu'une fois par période, au lieu de suivre chaque montée et
  descente du HEMS du fabricant, ce qui faisait osciller les deux. Le
  démarrage, l'arrêt, le passage en charge et les protections restent
  immédiats ; 0 = comportement précédent.
- Automatisations et notifications : une condition peut comparer une valeur
  à une autre variable (de la régulation ou de Home Assistant), avec un
  décalage facultatif (− 12 h, + 2 min, + 10). Les dates et heures se
  comparent dans le temps (saisie 01/11/2026 14:00 ou 22:30 acceptée), et
  une date peut être réduite à son jour, son heure, son jour de la semaine,
  aux heures restantes avant elle… Nouvelles variables : maintenant, date du
  jour, heure (HH:MM), jour du mois, mois.
- Nouveau modèle « Auto-calibration : batterie pleine avant » : dans les
  12 h qui précèdent l'auto-calibration prévue par un onduleur Zendure, il
  est désactivé pour que ses panneaux remplissent la batterie ; une règle
  par onduleur, à adapter (durée, batterie, production, météo…).
- Correction : en fin de journée, avec des batteries pleines qui laissent
  passer leur solaire, la régulation pouvait se figer avec le réseau en
  import (environ 250 W) ou osciller. La part de ces onduleurs est désormais
  leur sortie AC réelle (et non la production des panneaux) et elle est
  reprise dans la consigne suivante. Un onduleur dont la batterie stocke son
  solaire ne reçoit plus ce solaire en plus de sa part.
- Charge sur surplus solaire : les onduleurs sont choisis par niveau de
  batterie le plus bas, puis par solaire le plus faible (et non plus retirés
  par ordre alphabétique quand la consigne baisse : le plus chargé sort en
  premier). Un onduleur n'est retiré que lorsque la consigne passe nettement
  sous sa tranche (réglage « Écart avant de retirer un onduleur de la
  charge »), et la consigne passe progressivement d'un onduleur à l'autre,
  au rythme où le nouveau la prend vraiment (« Transfert progressif entre
  onduleurs »). Corrige une oscillation de la charge toutes les 30 s environ.
- Puissance réelle des batteries : la régulation repère une batterie qui
  accepte moins que solaire + consigne (limite du constructeur, niveau proche
  du maximum, température, batterie presque vide en décharge) et ne lui
  demande plus que ce qu'elle peut prendre ; le reste va aux autres
  onduleurs. La consigne ne monte plus dans le vide. Mesure prise dans la
  première entité disponible de l'onduleur : puissance entrant / sortant de
  la batterie, puissance prise au réseau (nouvelle entité facultative,
  Zendure : grid_input_power), puissance de la batterie, sortie AC. Sans
  aucune, rien ne change pour cet onduleur. Information « batterie limitée »
  dans la page Onduleurs (fonctionnement normal), lignes [LIMITE_BATT] dans
  le journal. Une batterie presque vide qui fournit une partie de sa consigne
  n'est plus mise de côté en « erreur 01 » (réservée aux onduleurs qui ne
  fournissent rien).
- Gain de correction de la décharge : 1 par défaut (réaction la plus rapide,
  tout l'écart au compteur est corrigé au recalcul suivant).
- Régulation plus réactive, sans rythme fixe : les réglages « Pause entre
  deux cycles » et « Délai entre deux changements de consigne » disparaissent.
  Dès qu'une nouvelle mesure du compteur arrive, la consigne est recalculée
  (une mesure inchangée ne relance rien). La part de consigne déjà envoyée
  qu'un onduleur n'a pas encore appliquée (il met 10 à 20 s à démarrer)
  n'est plus redemandée : fini la consigne qui monte deux fois puis le
  réseau qui part en export. Un onduleur ne reçoit une nouvelle valeur que
  si sa consigne change (moins de sollicitations de Home Assistant). Nouveau
  réglage avancé « Temps de réaction des onduleurs » (20 s).
- Nouveau réglage « Puissance souscrite de l'abonnement » (Paramètres ›
  Réseau électrique, aussi demandé par l'assistant de démarrage) : simple
  information, il ne limite rien.
- La vérification de la licence transmet aussi quelques statistiques
  anonymes : nombre d'onduleurs pilotés et surveillés, nombre
  d'automatisations, réseau monophasé ou triphasé et puissance souscrite.
  Jamais vos mesures ni votre consommation (détail : ted-energy.fr ›
  Confidentialité).
- Assistant de démarrage : l'étape Licence propose d'activer l'accès à
  distance pour l'application mobile (désactivé par défaut).
- Assistance à distance par le support TED (licences qui l'incluent) : une
  case à cocher (désactivée par défaut, Paramètres › Assistance et accès à
  distance ou assistant de démarrage) autorise le support à se connecter à
  votre installation pour vous aider : consultation, réglages et
  téléchargement des journaux. Aucun port à ouvrir : l'installation garde
  elle-même le contact avec ted-energy.fr. Chaque connexion vous est signalée
  (pastille « Support connecté », notification) et chaque modification est
  inscrite dans l'historique (« modification sur connexion distante »). La
  licence, le jeton Home Assistant et l'accès à distance restent réservés à
  vous. Décocher la case coupe l'accès immédiatement.
- Compte ted-energy.fr : la licence gratuite demandée depuis TED crée votre
  compte ; un e-mail de confirmation vous est envoyé (lien + mot de passe
  provisoire) et la licence s'installe toute seule dès que vous cliquez sur
  le lien. Réinstallation ou nouvelle machine : « J'ai déjà un compte »
  (connexion avec votre adresse et votre mot de passe, jamais gardé dans
  TED). Une licence déjà utilisée sur une autre machine : libérez-la dans
  votre espace client, TED réessaie tout seul. Une licence d'un compte
  importée à la main sur une nouvelle machine demande aussi la connexion.
  Si l'e-mail de confirmation ne peut pas être remis (adresse introuvable,
  boîte pleine), TED vous le dit tout de suite et propose « Corriger mon
  adresse » au lieu d'attendre une confirmation qui n'arrivera pas.
- Espace client ted-energy.fr : vos licences et machines, ce que chaque
  installation transmet, les demandes de prolongation, et (si vous cochez
  « Ouvrir cette installation depuis mon espace client ») l'accès à
  l'interface de votre installation de n'importe où, sans port ni VPN.
- Droits des licences : nombre d'automatisations actives (au-delà, les
  dernières ne sont pas exécutées), réseau triphasé inclus ou non (sinon
  régulation en monophasé), licences hors ligne (aucune vérification par
  Internet), reconduction automatique ou passage à une autre offre à la fin
  de la licence.
- Console d'entreprise (entreprises et collectivités multi-sites reliées par
  un VPN, licences hors ligne comprises) : sur un port dédié (5004, fermé par
  défaut), l'équipe informatique voit toutes les installations (état,
  licence, régulation) et ouvre leur interface, sans Internet. Chaque
  installation l'autorise et crée un code d'appairage ; chaque modification
  est inscrite dans son historique avec le nom de la personne (Paramètres ›
  Console d'entreprise).

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
