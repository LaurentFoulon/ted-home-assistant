# Journal des versions

Nouveautés détaillées de chaque version : <https://ted-energy.fr/evolutions>

## 3.7.1

- Onduleurs hybrides de toutes marques : la fiche commence par la marque et
  le modèle (obligatoires) et s'adapte à l'intégration Home Assistant de
  l'onduleur. Quatre façons de le commander : deux consignes (charge et
  décharge, avec ou sans sélecteur de mode), une consigne et un sélecteur de
  sens, une consigne signée (+ / −), ou des actions Home Assistant (Indevolt,
  Huawei…). Consigne en W, kW, % de la puissance maximale ou en ampères. Seul
  le niveau de batterie est obligatoire : les autres mesures sont
  facultatives, et la fiche indique les fonctions qui seront inactives sans
  elles. Sens de la puissance de la batterie réglable, plusieurs entrées
  solaires, puissance AC signée, entités en kW converties. Fiches de 20
  marques (Zendure, seule testée et validée ; Marstek, Indevolt, Anker SOLIX,
  EcoFlow, Growatt, Huawei, Sungrow, SolarEdge, Victron, Deye, GoodWe,
  Fox ESS, SolaX, Sigenergy, Solis, SMA, Fronius, Enphase, Tesla, avec leurs
  prérequis et leurs pièges connus) et « Autre marque », ouverte à tout.
  Réglages avancés par onduleur : temps de réaction et intervalle minimal
  entre deux consignes (intégrations lentes). Sans aucune mesure de
  puissance, le bilan est estimé d'après la consigne (« ≈ »). Les onduleurs
  Zendure déjà déclarés fonctionnent exactement comme avant.
- La configuration des onduleurs (marque, modèle, réglages, entités sans nom
  personnel, bon fonctionnement) est jointe à la validation de la licence,
  pour constituer la liste des onduleurs compatibles.
- Fiche d'un onduleur hybride : le niveau minimal pour décharger et le
  niveau maximal pour charger deviennent facultatifs. Laissés vides, ce sont
  les niveaux globaux (Paramètres › Batteries et puissances maximales) qui
  s'appliquent : les onduleurs d'autres marques, sans ce réglage, se
  déclarent sans rien inventer. Onduleur Zendure : un niveau modifié est
  écrit dans l'onduleur dès « Appliquer », sans message d'erreur (il
  fallait parfois cliquer plusieurs fois) ; un message n'apparaît que si
  l'écriture échoue.
- Correction : en choisissant l'appareil Zendure dans la fiche d'un onduleur,
  la puissance maximale de charge proposée était 8 000 W (borne générique de
  l'entité « input_limit ») au lieu de la vraie limite de l'appareil. Elle
  est maintenant lue dans son capteur de limite de charge (1 200 W sur un
  Hyper 2000), sans dépasser la puissance de l'onduleur.
- Nouveau réglage « Actualisation des variables » (Paramètres › Connexion
  Home Assistant, toutes les 60 minutes par défaut, 0 = jamais) : les
  niveaux de batterie renseignés dans la fiche des onduleurs Zendure sont
  relus dans Home Assistant ; une valeur changée dans l'application du
  fabricant est reprise dans la fiche et notée dans l'historique des
  modifications. Une valeur imposée par une automatisation n'est pas reprise.
- Nouvelle page « Équipements » (à côté de Onduleurs) : déclarez votre
  borne de recharge pour véhicule électrique, une prise domotique ou un
  autre appareil avec son entité de puissance de Home Assistant (W ou kW ;
  positive quand il charge ou consomme, négative quand il restitue, signe
  inversable). Le tableau montre la puissance en cours et l'énergie chargée
  et restituée depuis le nombre d'heures ou de jours choisi en haut à droite.
  Les équipements servent de condition dans les automatisations (puissance,
  en charge, énergie du jour). Ils ne changent pas la régulation des
  batteries.
- Automatisations et notifications : l'interrupteur d'une règle l'active ou
  la désactive tout de suite, sans devoir l'enregistrer ; ses autres
  modifications en cours ne sont pas enregistrées pour autant.
- Erreur 01 (l'onduleur accepte la consigne mais ne l'applique pas) : le
  test de déblocage se fait désormais dans le sens de la régulation, une
  petite charge quand les batteries se chargent (charge planifiée comprise),
  une petite décharge quand elles se déchargent. Un onduleur bloqué n'attend
  plus la prochaine décharge pour être débloqué. Puissance du test : 10 à
  50 W (50 W par défaut ; retirés aux autres onduleurs, la régulation
  n'oscille plus pendant le test). Le test réussit quand la puissance mesurée
  atteint la valeur demandée à 10 % près ; en charge, le solaire de
  l'onduleur est déduit (puissance prise au réseau).
- Nouvel état « Erreur 02 — test impossible pour l'instant », avec sa
  raison : batterie pleine pour une charge (à 2 % de son maximum : les
  onduleurs Zendure refusent parfois de charger à 88 % pour 90 %), batterie
  au minimum ou solaire qui traverse l'onduleur pour une décharge, aucune
  mesure… Le test part dès que la cause disparaît.
- Après un déblocage, l'onduleur ne repasse plus à 0 W puis en veille : il
  reste prioritaire pendant la « Durée minimale de travail d'un onduleur » et sa
  consigne monte progressivement (les autres onduleurs gardent la part
  qu'il ne prend pas encore).
- Décharge : un onduleur qui se met à décharger à côté d'un autre prend
  désormais sa part progressivement, comme en charge. Les onduleurs déjà en
  marche gardent leur consigne le temps qu'il démarre (10 à 20 s), au lieu
  de perdre leur part d'un coup, ce qui faisait osciller le réseau. Même
  chose quand un onduleur en remplace un autre ou s'arrête.
- Le réglage « Transfert progressif entre onduleurs » (Paramètres ›
  Répartition) s'exprime maintenant en pourcentage de la part du nouvel
  onduleur (25 % par défaut : 250 W par pas pour une part de 1000 W, 50 W
  pour une part de 200 W) et vaut pour la charge comme pour la décharge.
  L'ancienne valeur en watts n'est pas reprise.
- Menu ⋮ d'un onduleur en erreur 01 ou 02 : « Déblocage forcé (sans
  test) », quand le test est impossible ou que l'onduleur est reparti.
- Charge sur surplus solaire, correctif : avec un « Démarrage de la charge »
  très bas (10 W) et un délai court (10 s), la régulation pouvait passer de
  la décharge à la charge pendant de longues minutes la nuit. Le seuil est
  désormais de 100 W d'export au minimum (et au moins 100 W sous l'objectif
  au compteur du mode Autonome), le délai de confirmation de 15 s au
  minimum ; les valeurs plus basses déjà enregistrées sont relevées.
- Nouveau garde-fou « Charger seulement si les panneaux produisent »
  (activé par défaut) : la charge sur surplus ne démarre que si les panneaux
  déclarés (onduleurs pilotés, surveillés et onduleurs solaires) produisent
  au moins 200 W (réglable, 150 W au minimum : le talon de consommation de la
  maison). Sans onduleur avec panneaux déclaré, il ne s'applique pas. La
  charge planifiée n'est pas concernée.
- Expérimental, mode Autonome : « Charge optimisée », sur le principe du
  tir à la corde. Avec au moins
  deux onduleurs pilotés (et aucun onduleur surveillé), l'onduleur le moins
  chargé charge et le plus chargé décharge en permanence d'une petite
  puissance (50 W par défaut, de 0 à 50 W). La régulation ajuste une seule
  consigne nette autour de l'objectif au compteur : un surplus va à
  l'onduleur de charge, une consommation à l'onduleur de décharge, sans
  seuil, sans délai et sans arrêt ni démarrage d'onduleur à chaque nuage.
  Au-delà d'« Un onduleur de plus tous les… », d'autres onduleurs entrent
  du côté qui travaille ; un onduleur qui vient de travailler dans l'autre
  sens n'est repris qu'en dernier (durée minimale de travail). L'onduleur
  d'en face ne passe du même côté que si les autres ne peuvent plus fournir
  la puissance. Un onduleur qui atteint son niveau de batterie maximal
  devient aussitôt l'onduleur de décharge (l'ancien onduleur de décharge peut
  alors charger) et ne recharge qu'après être redescendu de 3 points. Le mode
  ne démarre que sur un vrai surplus (pas pendant une décharge qu'il suffit
  de baisser). La charge optimisée s'arrête après 2 minutes sans charge
  nette (réglable de 30 s à 5 min ; la décharge standard reprend à la même
  puissance) ou quand moins de
  deux onduleurs peuvent charger et décharger. Contreparties : un peu
  d'énergie passe en permanence d'une batterie à l'autre, et le tableau de
  bord affiche une petite décharge pendant la charge. Désactivé par défaut.
- Page Paramètres refaite : la liste des rubriques reste visible sur le
  côté, une seule rubrique est affichée à la fois, la recherche porte sur
  tous les réglages. Chaque réglage se lit comme une phrase, avec sa valeur
  par défaut et « Rétablir » juste dessous ; les réglages fins sont repliés
  dans chaque rubrique. Des illustrations animées montrent l'effet des
  réglages sur vos propres onduleurs (charge optimisée et charge standard
  comparées, répartition sur une journée, délestage de la charge planifiée,
  triphasé…). La licence a sa propre rubrique.
- La « Puissance souscrite » de votre abonnement (rubrique Abonnement et
  tarifs) plafonne désormais les puissances prises au réseau : charge des
  batteries, charge planifiée et, en triphasé, puissances par phase (le
  tiers de l'abonnement). Par défaut, elles valent la puissance de
  l'abonnement ; sans abonnement renseigné, 3 000 W par prudence. La
  décharge totale n'en dépend pas (elle alimente la maison sans passer par
  le compteur). Mise à jour : vos valeurs actuelles sont gardées ; si
  l'abonnement n'est pas renseigné, un bandeau le demande sur le tableau de
  bord.
- Une seule adresse de Home Assistant : l'ancienne « adresse IP + port » est
  reprise automatiquement dans l'adresse (par exemple
  http://192.168.1.50:8123).
- Shelly Pro 3EM en profil triphasé : il est maintenant lu aussi en réseau
  monophasé (auparavant, seul le profil monophasé l'était). Le bouton
  « Tester et choisir » (Paramètres › Connexion) affiche la puissance de
  chaque phase, mise à jour toutes les 2 secondes, et propose la bonne :
  choisissez la phase 1, 2 ou 3, ou la somme des trois.
- Lissage renforcé en cas d'oscillation : nouvel interrupteur (Paramètres ›
  Rythme et stabilité). Il est coupé à la mise à jour s'il était sans effet.
- Nouvelle rubrique « Régler avec une IA » : un texte prêt à copier pour
  ChatGPT, Claude, Le Chat ou Gemini (fonctionnement du logiciel, votre
  installation, vos réglages) et le résumé de vos journaux à joindre (une
  ligne par minute et les événements, sans adresse ni identifiant), pour vous
  aider à régler l'installation. Rien n'est envoyé : c'est vous qui collez.
- Plages des réglages revues (par exemple 100 à 6 000 W pour la hausse et la
  baisse de la décharge) : une valeur enregistrée hors de la nouvelle plage
  est ramenée à la borne la plus proche.

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
