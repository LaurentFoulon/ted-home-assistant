# Journal des versions

Nouveautés détaillées de chaque version : <https://ted-energy.fr/evolutions>

## 3.8.1

- **« Où passe l'énergie » : un jour au choix ou une durée, aujourd'hui en direct.** Le diagramme se pilote comme
  l'historique détaillé : **Jour** (flèches, calendrier, bouton « Aujourd'hui »), **6 h**, **24 h**, **7 jours** ou
  **30 jours** qui se terminent maintenant. Plus aucune attente : la journée en cours est calculée avec les mesures déjà
  enregistrées et se met à jour chaque minute. Même graphique et même contenu pour toutes les périodes ; l'export CSV
  couvre les jours entiers de la période. Nouvelle forme de la route : `GET /api/energy/flows?start=&end=` (millisecondes).
- **Widget « Solaire : prévu et réel » refait.** Frise des 7 derniers jours et de demain (un clic ouvre le jour ; la
  pastille donne l'écart de la prévision de la veille), avancement de la journée en cours (« 75 % de la prévision, reste
  ≈ 1 kWh à produire »), pic, ciel, fiabilité de la prévision sur 30 jours, heure de batteries pleines (avec fourchette)
  et courbe lisible heure par heure en la survolant ou en la touchant (réel, prévu la veille, corrigé).
- **Partage d'automatisations et bibliothèque de la communauté.** Page Automatisations : **Exporter** (fichier
  `.tedauto.json` ou texte à copier, dans la fenêtre d'une automatisation) et **Importer** (fichier ou texte collé). À
  l'export, les adresses web et leurs en-têtes, e-mails, adresses IP et jetons sont retirés ; les entités et onduleurs
  visés restent visibles. À l'import, un assistant associe chaque équipement de l'auteur (entité, onduleur, cluster,
  zone, équipement, valeur d'appareil) à l'un des vôtres ; l'automatisation est toujours créée **désactivée**. Les
  actions qui appellent un service, un script, une scène ou une adresse web sont signalées en rouge (non contrôlées) ;
  des services dangereux (`shell_command`, redémarrage de Home Assistant…) sont refusés. **Bibliothèque** (réservée aux
  utilisateurs connectés, avec la licence de l'installation) : parcourir par catégorie, recherche, tri par
  téléchargements, étoiles ou nouveautés ; importer, donner une étoile (une par compte), signaler ; **Publier** une
  automatisation avec titre, catégorie, mots-clés et description (français ou anglais). Chaque automatisation a un
  identifiant unique : modifiée après publication, TedEnergy propose de mettre la publication à jour (nouvelle version
  avec journal des changements, ou la garder telle quelle) ; une automatisation importée signale la nouvelle version de
  l'auteur (« Mise à jour vN », associations reprises). Publier demande un compte ted-energy.fr et un **pseudonyme**
  (seul nom affiché). Nouveau module `automation_share.py`, routes `/api/automations/export`, `/import/*`,
  `/api/library/*` (jamais accessibles à distance), état dans `runtime/automation_library.json`.
- **Compteur électrique : plusieurs compteurs classés par priorité, dont une valeur MQTT.** Nouvelle rubrique
  Paramètres › Mon installation › Compteur électrique (séparée de la connexion Home Assistant, qui reste dans
  Appareils et connexions) : un ou plusieurs compteurs, dans l'ordre de priorité. Trois types : entité Home Assistant,
  **valeur d'un appareil relié à TedEnergy** (serveur MQTT intégré, cloud ou API locale d'un fabricant : par exemple le
  Smart Meter 3CT de Zendure, sans Home Assistant) et Shelly en direct. Si le compteur utilisé ne répond plus pendant
  5 s, TedEnergy passe sur le suivant ; il revient sur le prioritaire quand celui-ci répond sans interruption depuis
  5 min (délais réglables dans Communication avancée). Option « Inverser le signe » pour un appareil qui compte
  l'injection en positif. L'assistant de démarrage peut être passé sans compteur. Les installations existantes ne
  changent pas : leur compteur actuel devient le compteur n° 1.
- **Connecteurs d'appareils : Indevolt et IsyPower ne sont plus expérimentaux** (API locale officielle, vue fonctionner en
  réel), et **série Zendure SolarFlow Mix reconnue** (3000 Mix AC+, 4000 Mix AC+, 4000 Mix Pro) par le cloud Zendure et le
  remplissage automatique ; non encore confirmée sur un appareil réel. Catalogue des marques mis à jour : Anker SOLIX a un
  Modbus TCP local officiel (Solarbank Max, XE, 4 E5000 Pro), Victron expose le MQTT et le Modbus TCP, Sigenergy, Sessy et
  GivEnergy ajoutés.
- **Diagnostic › Sonde Modbus** : TedEnergy lit, en lecture seule, les registres d'un appareil Modbus TCP du réseau local
  (cartes Marstek Venus, Victron GX, Anker SOLIX, ou plage libre) et affiche les valeurs brutes. C'est aujourd'hui une façon de
  tester des appareils pour valider leurs registres avant de les intégrer dans les connecteurs. Résultat copiable ou enregistrable
  sans l'adresse de l'appareil, résumé dans le journal (`[SONDE_MODBUS]`) ; le support peut la lancer quand l'assistance à
  distance est ouverte. Les utilisateurs qui la lancent peuvent écrire à contact@ted-energy.fr pour aider à fiabiliser leur marque.
- **Charge planifiée : toutes les batteries finissent ensemble.** Quand le réseau limite la charge (12 kW pour 12 kVA), la
  puissance était partagée selon la puissance maximale de chaque onduleur : les petites batteries se remplissaient en premier
  et les grandes restaient à 70 % (relevé du 08/11 : de 70 % à 98 % à la fin du créneau). Elle est maintenant partagée selon
  l'énergie qu'il reste à stocker dans chaque batterie, sans jamais dépasser la puissance de charge de chaque onduleur : toutes
  arrivent au même niveau (environ 85 % dans le même cas), pour la même énergie stockée. Réglage « Finir la charge tous
  ensemble » (Paramètres › Charge planifiée), actif par défaut.
- **Préparation de la charge planifiée (Store) plus fiable** : niveaux attendus par onduleur à la fin du créneau (calculés avec
  la même règle de partage), niveaux prévus au début du créneau d'après la consommation moyenne et la prévision solaire, budget
  de charge basé sur la puissance souscrite complète (12 kVA = 12 kW) moins la maison, gain minimal de 0,5 kWh pour intervenir
  (jamais en dessous), calcul qui doit rester utile 60 s avant de démarrer et décision maintenue 5 minutes une fois lancée.
  Chaque décision est écrite dans le journal (`[PREPARATION]`, détail dans `preparation_journal.jsonl`), avec la comparaison
  niveaux prévus / niveaux atteints à la fin du créneau.
- Nouveau dans le Store (à tester) : **Réserve de secours**. TedEnergy lit les rafales de vent et les orages des trois
  prochains jours dans la prévision météorologique (Open-Meteo, MET Norway). Quand une tempête est annoncée (seuil de rafales
  réglable, 90 km/h par défaut, orages en option), ou à la date d'une coupure programmée que vous indiquez, les batteries sont
  préparées N heures avant le début (12 h par défaut) : décharge bloquée, niveau maximal temporaire (100 % par défaut), charge
  depuis le réseau en heures creuses puis dans les 3 dernières heures si besoin (ou toujours, ou jamais). La réserve est gardée
  jusqu'à la fin annoncée de l'événement, puis tout redevient comme avant. Une tempête qui n'est plus annoncée avant d'avoir
  commencé est annulée. TedEnergy ne bascule jamais l'installation hors réseau : c'est l'onduleur qui le fait s'il en est capable.
- Nouveau dans le Store (à tester) : **Vente du surplus au bon moment**, pour les contrats qui rachètent l'électricité plus cher
  que vous ne l'achetez à certaines heures. En dehors des moments de vente, la décharge est bloquée (ou régulation habituelle, au
  choix) ; pendant la vente, l'objectif au compteur devient une injection fixe (3 kW par défaut : la maison consomme 1 kW, les
  onduleurs déchargent 4 kW). Moments de vente : créneaux horaires (jours de la semaine, passage de minuit), une entité de Home
  Assistant allumée, ou une quantité d'énergie (kWh) demandée par une entité : TedEnergy la livre puis s'arrête, même après un
  redémarrage. Puissance fixe ou lue sur une entité, plafonnée par la puissance souscrite et par « Respect de l'injection
  réseau » ; vente arrêtée au niveau gardé (20 % par défaut) ; kWh vendus comptés.
- Store : **Veille de jour rouge**, **Mode vacances** et **Alertes d'anomalie** vérifiées et sorties du bloc « À tester ». Les idées
  « Bientôt dans le Store » sont retirées. Corrigé : une fois le niveau atteint pendant les heures creuses, la veille de jour rouge
  et la charge de nuit selon la météo gardent l'énergie chargée (décharge bloquée jusqu'à la fin des heures creuses) au lieu de la
  laisser partir dans la maison puis de recharger aussitôt (allers-retours et pertes) ; la charge ne reprend que 3 points plus
  bas. La charge de nuit « à toute heure » ne charge plus que la nuit. Après un redémarrage, le bilan des vacances et une alerte
  déjà signalée ne sont plus renvoyés.
- Refait : **prévision de la production solaire apprise sur votre installation**. Plus rien à déclarer (puissance,
  inclinaison, orientation des panneaux) : TedEnergy apprend, heure par heure, comment VOTRE installation répond au
  soleil direct et à la lumière des jours couverts, à partir de sa production réelle. Orientation, ombrages des arbres ou
  des voisins et pertes sont compris ; une matinée claire suffit même si l'après-midi est nuageux ; l'apprentissage continue
  chaque jour (panneaux ajoutés ou retirés, feuillage selon la saison) et la courbe se recalcule pour les jours plus courts
  de l'hiver. La courbe « journée sans nuage » affichée est celle de votre installation, plus une courbe théorique.
- Nouveau : **rayonnement mesuré par satellite** (EUMETSAT via Open-Meteo, Europe, Afrique et Asie ; case « Mesure
  satellite », activée par défaut). Il voit le brouillard et les nuages bas que les modèles météo prévoient mal, sert à
  reconnaître les heures vraiment claires et à corriger la journée quand les batteries pleines brident la production.
- Nouveau : **correction de la journée en cours**. Dès qu'il y a assez de lumière, la prévision d'aujourd'hui suit la
  production réelle des deux dernières heures (essai sur 15 jours : l'écart sur le reste de la journée passe de 46 % à
  23 %). Nouvelles variables `meteo.solar_today_dayahead_kwh` (prévue la veille) et `meteo.correction_pct` ;
  `meteo.solar_today_kwh` et `meteo.solar_remaining_today_kwh` sont désormais corrigées.
- Nouveau : **historique des prévisions** (bouton « Historique des prévisions » de la page et du widget) : pour chaque
  jour, la prévision de la veille (figée à minuit), la prévision corrigée dans la journée (chaque heure figée à son
  début) et la production réelle, heure par heure, avec l'écart en %. Le widget « Solaire : prévu et réel » passe d'un
  jour à l'autre avec ‹ ›.
- Amélioré : les **sources météo** se combinent en donnant plus de poids à celles qui se sont le moins trompées chez vous
  (après 5 jours comparés) ; sources conseillées selon votre région (pays nordiques : MET Norway d'abord).
- Nouveau : **durée minimale d'activation d'une automatisation** (« Rester active au moins », en minutes, à côté du
  « Délai minimal entre deux débuts »). Une fois commencée, l'automatisation garde ses effets pendant cette durée, même si la
  condition redevient fausse ; ensuite elle se termine normalement. « Terminer quand elle est fausse depuis au moins » ne
  suffisait pas : si la condition redevenait vraie à temps, l'automatisation ne se terminait jamais. À 0, rien ne change.
- Amélioré : **niveau moyen des batteries des onduleurs pilotés** (variable des automatisations) = vraie valeur, pondérée par
  la capacité en kWh de chaque onduleur (1 kWh vide + 4 kWh pleins = 80 %, pas 50 %), comme le niveau global du widget « Énergie
  des batteries ». La moyenne simple n'est utilisée que tant que la capacité d'un onduleur est inconnue (batteries non
  déclarées). La mention « moyenne simple » est retirée du widget.
- Amélioré : tableau des **équipements** : « Puissance instantanée » et « Consommé » ; la colonne « Restitué » n'apparaît que
  si une borne bidirectionnelle (V2G) est déclarée.
- Nouveau : **protection du tableau électrique contre la surcharge** (activée par défaut). Le disjoncteur général ne voit
  que le courant qui vient du réseau : les onduleurs injectent en aval de lui. Avec 6 kW de décharge et 10 kW du réseau, le
  tableau porte 16 kW sans que rien ne coupe. TedEnergy surveille maintenant ce qui passe réellement dans le tableau (réseau
  + sortie des onduleurs hybrides + micro-onduleurs ; le solaire d'un hybride n'est pas compté, seule sa sortie l'est) et,
  au-dessus de **votre abonnement + 10 %** pendant 20 s sans interruption, **coupe la décharge de vos batteries** : la maison
  repasse sur le réseau et le disjoncteur général retrouve son rôle de protection. Une barre rouge en haut de toutes les pages
  dit pourquoi, et un bouton « Relancer la régulation » (avec confirmation) permet de reprendre tout de suite ; sinon la
  décharge reprend seule quand la consommation reste 2 minutes sous 90 % du seuil. Une surcharge franche (plus de 150 % du
  seuil) est coupée après 10 s. En triphasé, le calcul se fait phase par phase et seuls les onduleurs de la phase concernée
  sont coupés. La désactiver (Paramètres › Abonnement et tarifs) demande de confirmer que l'on comprend le risque.
  Nouvelles variables d'automatisation `panel.*`. TedEnergy n'agit que sur vos **onduleurs pilotés** : les onduleurs
  surveillés et les micro-onduleurs comptent dans la mesure mais restent hors de son contrôle (le disjoncteur général
  protège alors seul). Un nouveau widget **« Charge du tableau »** (bibliothèque du tableau de bord) montre ce qui passe
  dans le tableau comme un compteur de vitesse, avec la limite avant la coupure.
  **Effet à connaître :** avec un abonnement de 6 kVA, un pic de consommation au-dessus de 6,6 kW, auparavant complété par
  les batteries, coupe désormais la décharge : renseignez la puissance réelle de votre abonnement. Si votre tableau, vos
  câbles et vos disjoncteurs supportent plus que l'abonnement, faites-les vérifier par un électricien puis déclarez la
  puissance acceptée dans le Store (« Capacité de mon tableau électrique ») : la décharge pourra s'ajouter au réseau
  jusqu'à cette puissance.
- Nouveau (Store, à tester) : **« Capacité de mon tableau électrique »**. Pour un tableau, des câbles et un jeu de barres
  qui supportent plus que l'abonnement (abonnement 6 kVA, tableau prévu pour 12 kW), vous déclarez la puissance que votre
  tableau peut porter (après contrôle par un électricien) : elle devient la base du seuil de coupure, toujours avec les 10 %
  de marge. Désactivée, la protection revient à l'abonnement. Le réseau reste limité par l'abonnement.
- Corrigé : **décharge emballée à la fin de la charge planifiée**. Les onduleurs mettent 10 à 20 secondes à
  arrêter leur charge, et la régulation prenait cette charge encore en cours pour une consommation de la maison :
  jusqu'à 7 kW de décharge sur tous les onduleurs pour quelques centaines de watts de besoin, puis plusieurs kW
  renvoyés au réseau. La charge en train de s'arrêter n'est plus comptée : la décharge reprend directement à la
  consommation de la maison, sur le nombre d'onduleurs nécessaire (aussi à la fin d'une charge imposée par une
  automatisation).
- Corrigé : **tous les onduleurs arrêtés d'un coup puis relancés quelques secondes plus tard**. Pendant une forte
  décharge, le petit export dû à la régulation elle-même suffisait à préparer la charge : au bout de la temporisation,
  la décharge était coupée sur tous les onduleurs, puis relancée au cycle suivant (avec un achat au réseau le temps du
  redémarrage). La charge ne se prépare désormais qu'une fois la décharge redescendue à son minimum.
- Nouveau : **aucun onduleur ne décharge sous 50 W** (consigne de réveil), même prioritaire : quand la consigne est
  faible, elle va à moins d'onduleurs au lieu d'être partagée en miettes (5 W chacun auparavant).
- Changé : la **durée minimale de travail d'un onduleur** (ancien libellé « Priorité après déblocage ») est comptée
  depuis la dernière fois où l'onduleur était nécessaire, comme pour la priorité solaire, et elle est respectée aussi
  quand les onduleurs en priorité solaire suffisent. Un onduleur s'arrête plus tôt seulement si la charge démarre, si
  sa part de la consigne passerait sous 50 W, ou par une protection.
- Corrigé : **impossible de retirer une connexion MQTT désactivée ou hors ligne** d'un appareil, ni de supprimer l'appareil MQTT
  qu'elle citait : les deux se bloquaient. Règle unique et claire : un appareil utilisé par la régulation garde toujours au
  moins une connexion utilisable. Toute autre connexion se retire (corbeille de la fiche, ou « Détacher de… » directement
  depuis la liste de la Connexion MQTT et des connecteurs) ; la suppression d'un appareil MQTT, d'un appareil de connecteur ou
  d'un compte, citée par des appareils qui gardent une autre connexion, demande confirmation puis détache ces appareils tout
  seule. La seule connexion utilisable d'un appareil reste protégée, avec la marche à suivre et un accès direct à sa fiche.
- Nouveau : **rattacher une connexion à un appareil en un clic**. Un appareil MQTT, du cloud ou en API locale « sans appareil
  associé » se rattache directement depuis sa ligne (bouton « Rattacher à un appareil »), depuis un bandeau de Paramètres ›
  Appareils quand de nouveaux appareils MQTT sont détectés, ou depuis le menu ⋮ : on choisit l'appareil (celui du même modèle
  est proposé d'abord, ou « Nouvel appareil »), sa fiche s'ouvre sur la nouvelle connexion avec l'appareil déjà choisi et ses
  entités proposées ; il ne reste qu'à tester puis appliquer. Les listes indiquent par quel appareil chacun est utilisé.
- Nouveau : la **maison 3D tient compte de la météo** (si la prévision météorologique du Paramètres › Mon installation est
  activée) : lumière du soleil adoucie, ciel et brouillard ternis, ombres de nuages qui glissent sur la pelouse, et mention
  « Ciel dégagé / Quelques nuages / Nuageux / Ciel couvert » sous la production solaire. La nébulosité de l'heure est publiée dans
  `state.weather` (`cloud_pct`) : l'application Android 2.2 affiche la même maison.
- Nouveau : **Store de fonctionnalités**. Une nouvelle page « Store » regroupe des fonctionnalités natives, éprouvées, que
  vous activez ou désactivez en un clic ; chacune s'explique par un **schéma animé**, ne s'active que si ses conditions sont
  réunies (onduleur compatible, entité disponible, licence) et s'appuie sur le moteur des automatisations. Deux
  fonctionnalités pour commencer : **Calibration programmée** (à la date et l'heure choisies, la décharge des onduleurs
  choisis, ou de tous, est bloquée et leur niveau maximal passe à 100 % pendant les heures qui précèdent ; répétition
  possible) et **Respect de l'injection réseau** (en journée, quand le solaire produit : au-dessus de la puissance
  injectée choisie, mesurée au compteur, 3 kW par défaut, les onduleurs sont
  désactivés un à un, le plus gros d'abord, et leur contournement vers le réseau est bloqué ; la décharge des batteries
  vers la maison n'est jamais comptée, la fonction reste en veille la nuit ; entité détectée pour Zendure,
  à choisir pour les autres marques ; retour progressif sans va-et-vient). Le droit « Store » et le nombre de
  fonctionnalités actives sont réglables par licence. **Calibration programmée** suit par défaut la **date annoncée par
  l'onduleur** (Zendure : « Next Calibration », cloud, API locale ou Home Assistant), onduleur par onduleur ; les réglages
  affichent maintenant, pour chaque onduleur, la **variable lue et sa valeur du moment** (« sensor.cloud_zendure_b1_next_calibration
  = dim. 29/11 à 17:31, dans 27 jours ») ; ceux qui n'en annoncent pas suivent une date saisie, avec répétition. Correction :
  la variable n'était jamais retrouvée sur une fiche complète (ni sur une autre connexion que celle utilisée, ni avec un
  fuseau horaire différent de celui de la machine). Les modèles d'automatisation « Auto-calibration », « Jour Tempo rouge
  demain » et « Prévision solaire faible » sont supprimés : ils font double emploi avec le Store.
  Le message de la **Préparation de la charge planifiée** indique aussi la puissance du réseau et la place disponible
  quand c'est la puissance qui limite.
- Nouveau : **fonctionnalités « à tester »** dans le Store, regroupées dans un bloc à part (confirmation avant activation) :
  **Veille de jour rouge** (la nuit qui précède un jour Tempo rouge, ou rouge et blanc, charge en heures creuses jusqu'au niveau
  choisi ; option « seulement si peu de soleil est prévu »), **Charge de nuit selon la météo** (charge en heures creuses
  seulement ce que le soleil de demain ne fournira pas, d'après la prévision solaire, la capacité des batteries et la
  consommation moyenne apprise sur 7 jours ; ou niveau fixe quand peu de soleil est prévu), **Surplus vers le ballon d'eau
  chaude** (allume un interrupteur de Home Assistant avec le surplus solaire, avant les batteries ou après un niveau choisi,
  seuils et délais anti-va-et-vient), **Mode vacances** (dates d'absence : niveaux minimal et maximal imposés, charge depuis le
  réseau coupée pour toutes les fonctions du Store, bilan de l'absence à votre retour) et **Alertes d'anomalie** (autant
  d'alertes que voulu : conditions, durée, message avec valeurs, journal des notifications et téléphone ; sept exemples).
  **Préparation de la charge planifiée** n'est plus signalée expérimentale. Le choix d'une entité Home Assistant (interrupteur du
  ballon) reste réservé à l'interface web, comme celui du blocage du contournement.
- Nouveau : **menu « Appareils »**. Onduleurs et Équipements partagent un seul onglet, avec une liste déroulante pour passer
  de l'un à l'autre ; les onglets suivants sont Store, Paramètres, Diagnostic, Automatisations, Notifications et Aide.
- Nouveau : **tableau de bord à widgets**. Chaque bloc (flux d'énergie en direct avec la maison complète, « En ce moment »,
  bilan du jour, derniers jours, « Où passe l'énergie », historique) est un widget. « Modifier le tableau » permet de
  **les déplacer en maintenant le clic**, de les **redimensionner** par le coin, de **changer leur taille**, de les
  **retirer** ou de les **masquer sur téléphone**. « Ajouter un widget » ouvre une **bibliothèque** qui liste tous les
  widgets, affichés ou non, avec un aperçu calculé sur vos mesures avant l'ajout.
- Deux modes de placement au choix : **Aligné** (les widgets se rangent ligne par ligne, dans l'ordre, sans trou) et
  **Libre** (chaque widget reste où vous le posez, des espaces vides sont possibles ; deux widgets ne se chevauchent
  jamais). Sur tablette (2 colonnes) et téléphone (1 colonne), les widgets s'empilent dans l'ordre de la liste.
- Nouveaux widgets : **Économies** (en euros, jour par jour), **Prochaine période tarifaire** (prix du kWh, temps avant
  le changement, journée en un coup d'œil), **Solaire : prévu et réel**, **Santé des batteries**, **Heures de
  consommation** (7 jours × 24 h), **Qui consomme** (par équipement), **Pic de consommation** (contre la puissance
  souscrite) et **Valeur au choix** (n'importe quelle variable de TedEnergy, en plusieurs exemplaires).
- La disposition est unique pour l'installation, **enregistrée sur le serveur** dans la configuration (historique des
  modifications, sauvegarde) et lisible / modifiable par l'application mobile (`/api/dashboard/layout`, avec numéro de
  révision : une modification faite ailleurs entre-temps est signalée). Un widget ou un réglage inconnu (version plus
  récente) est conservé.
- Application mobile (accès à distance) : l'application peut désormais lire les flux d'énergie (`/api/energy/flows`),
  commander la borne de recharge (Charger maintenant / Pause / Automatique) et reprendre la main sur un onduleur dont la
  consigne a été changée par un autre système (ou effacer le rappel de l'incident). Les réglages et la déclaration des
  équipements restent réservés à l'interface locale.
- Nouveau : **énergie réelle des batteries**. Quand les batteries d'un onduleur sont déclarées avec leur capacité (fiche de
  l'appareil › Batteries ; pour une batterie Zendure AB1000S, AB2000X, AB3000… la capacité est déduite du modèle), TedEnergy
  connaît l'**énergie** de chaque onduleur en kWh et pas seulement son niveau en %. Le **niveau global est pondéré par les
  capacités** : une batterie de 4 kWh pleine et une de 1 kWh vide font 80 %, et non plus 50 % (scène de la maison, total de
  la page Onduleurs ; la régulation continue de lire les niveaux des onduleurs). Un onduleur sans capacité connue n'est pas
  compté et le niveau global reste alors la moyenne simple.
- Nouveaux widgets : **Énergie des batteries** (niveau pondéré, énergie stockée, utilisable jusqu'aux niveaux minimaux, place
  restante, détail par onduleur), **Autonomie des batteries** (temps et heure avant batteries vides en décharge, ou
  pleines en charge, à la puissance moyenne des dernières minutes) et **Batteries pleines à…** (heure estimée de batteries
  pleines d'après la production solaire prévue par la météo et la consommation moyenne de la maison, avec sa fourchette et
  la courbe du niveau attendu ; une estimation, sans certitude). Les réglages sont dans le widget, pas dans les Paramètres :
  **puissance moyenne sur 10, 15, 20 ou 30 minutes** et **actualisation toutes les 1 à 15 minutes**. Les nouveaux
  réglages propres à un widget s'ouvrent par l'icône « réglages » du mode « Modifier le tableau » et dans la bibliothèque.
- Nouvelles variables pour les automatisations : `battery.soc_weighted_pct`, `battery.capacity_kwh`, `battery.energy_kwh`,
  `battery.usable_kwh`, `battery.room_kwh`, `battery.autonomy_h`, `battery.full_in_h`.
- Nouvelle fonctionnalité du Store (expérimentale) : **Préparation de la charge planifiée**. Quand les batteries n'ont pas
  la même capacité, la charge planifiée ne les remplit pas toujours à fond dans le temps imparti (puissance de charge de
  chaque onduleur, délestage de la puissance souscrite). Avant le créneau, la fonction calcule ce que chaque batterie pourra
  absorber et, si une batterie ne sera pas pleine à temps alors qu'une autre a de la marge, déplace de l'énergie des
  batteries bien remplies vers les plus vides (certaines chargent pendant que d'autres déchargent) tout en continuant de
  lisser le réseau à l'objectif ; un pic de consommation est complété par les autres onduleurs. Elle n'intervient que si cela
  permet de stocker davantage, et prévient que ce transfert consomme un peu plus d'énergie (conversions DC/AC, effet Joule).
  Réglages : délai de préparation (1 à 12 h), part de la puissance souscrite (80 % par défaut), onduleurs concernés,
  puissance de transfert et gain minimal. Chaque onduleur affiche son rôle : « Préparation de la charge planifiée : cède de
  l'énergie… / reçoit de l'énergie… ». Option **« Transférer avec le solaire seulement »** : aucune batterie n'est déchargée
  pour en charger une autre. L'onduleur qui cède sort le solaire qu'il produit (sa batterie ne charge ni ne décharge) et la
  batterie presque vide le reçoit en plus de son propre solaire (A produit 200 W et sort 200 W, B produit 100 W et charge
  300 W) ; le transfert démarre à partir de 80 W de solaire sur les onduleurs qui cèdent, s'arrête sous 30 W, et la
  consommation de la maison reste couverte comme d'habitude. Ménage la durée de vie des batteries.

## 3.7.11

- Nouveau : **flux d'énergie sur 24 heures** (hier et avant-hier) pour un diagramme de Sankey : d'où vient l'énergie
  (solaire, réseau par période de l'abonnement, décharge de la batterie) et où elle va (maison, charge de la batterie,
  injection). Le solaire alimente d'abord la maison, puis charge la batterie, puis part sur le réseau ; le manque est
  comblé par la batterie, puis par le réseau. Le tableau de bord affiche la carte **« Où passe l'énergie »** (diagramme de
  Sankey, au choix hier ou avant-hier, avec un tableau de tous les flux) ; les mesures sont aussi disponibles par
  `/api/energy/flows` et en CSV horaire (`/api/energy/flows.csv`).
- Vérifié : la charge planifiée de nuit (réseau vers batteries) n'est **pas** comptée dans la consommation de la maison
  (elle apparaît comme charge de la batterie alimentée par le réseau). Le bilan horaire solaire + achat + décharge =
  maison + charge + injection est contrôlé et son écart est affiché.

## 3.7.10

- Corrigé : sur la maison animée, un équipement spécifique (pompe à chaleur, ballon d'eau chaude, piscine) dont la
  puissance est sous le seuil d'affichage reste dessiné, mais sans cadre ni puissance (avant, le cadre « 0 W · au repos »
  restait affiché).
- Automatisations : **elles reprennent après un redémarrage de TedEnergy**. L'état de chaque automatisation (active
  depuis quand, dernier début) est conservé ; pendant les 2 premières minutes une automatisation active n'est pas
  terminée (les mesures du cloud ne sont pas encore revenues) et ses délais ne repartent plus de zéro.
- Automatisations : nouvelle case **« Si une valeur de la condition est indisponible, garder l'état actuel »** (ni début ni
  fin tant qu'une valeur manque : entité de Home Assistant ou cloud qui disparaît quelques instants).
- Automatisations : **nouvelles variables** pour les conditions : état de chaque onduleur (erreur 01, calibration, en
  pause, démarrage, état, connexion utilisée, connexion de secours), lever et coucher du soleil, il fait jour, hauteur du
  soleil (selon la position réglée dans la prévision météo), bilan du jour (production, consommation, achat, injection,
  autonomie, coût net) et prochaine période tarifaire (heures avant le changement, avant les heures creuses, avant leur
  fin).
- Automatisations : l'action **adresse web (webhook)** accepte une méthode (POST, GET, PUT, PATCH), un corps et des
  en-têtes, avec {rule}, {event}, {time} et n'importe quelle variable ({battery.avg_pct}) : notifications ntfy, Telegram,
  Discord… Sans option, le message habituel est envoyé comme avant.
- Automatisations : la liste affiche **la dernière activité** de chaque automatisation (début, fin, nombre de débuts et
  durée de marche sur 7 jours) ; la fenêtre de l'automatisation donne l'**historique** des débuts et fins et une
  **simulation sur une journée passée** (hier, aujourd'hui ou une date) avec les mesures enregistrées et les délais réglés,
  sans rien appliquer ; les valeurs que l'historique ne garde pas (entités de Home Assistant, cloud…) sont signalées.
- Corrigé : dans l'éditeur d'une automatisation, la liste déroulante de choix d'une variable ou d'une entité ne se ferme
  plus quand on fait défiler la molette avec le curseur hors de la liste. Elle reste ouverte, suit son bouton, et ne se
  ferme qu'au choix d'une valeur ou à un clic à côté.
- Amélioré : **« Régler avec une IA »** à jour des dernières évolutions. Le résumé joint à l'IA commence maintenant par
  l'installation (onduleurs, connexion utilisée, priorités, marges, équipements, automatisations), le bilan de chaque
  jour, l'activité de chaque onduleur (heures, démarrages, défauts, pauses, écritures en échec) et les réglages que vous
  avez modifiés pendant la période : l'IA voit ce qui est installé et l'effet de ses conseils. Le texte donné à l'IA
  décrit le démarrage des onduleurs, les priorités de décharge, les marges de redémarrage, les connexions multiples, les
  équipements et les automatisations (effets et variables), et lui demande de vérifier la santé de l'installation avant
  de proposer des réglages ou des automatisations. Les noms anonymes (« Onduleur 1, 2… ») s'appliquent aussi au résumé.
- Nouveau : **la phase d'un onduleur piloté est détectée automatiquement** (réseau triphasé). Elle n'est plus à saisir :
  la fiche de l'onduleur l'affiche en lecture seule (« détectée le … à … W »). Le bouton « Identifier la phase »
  (fiche de l'onduleur, menu ⋮ de la page Onduleurs) la refait à volonté : l'onduleur décharge brièvement et TedEnergy
  regarde quelle phase du compteur réagit. Si le résultat n'est pas sûr (petite puissance, appareil de la maison qui
  démarre), la puissance monte (300, 700, 1 200, 2 000 W au plus, dans la limite de l'onduleur) jusqu'à ce que la phase
  soit certaine. Le test de validation (Diagnostic › Test des onduleurs) la détecte aussi, et lance cette recherche à
  puissance croissante quand ses 100 W ne suffisent pas. Les onduleurs surveillés et solaires, qui ne reçoivent aucune
  consigne, gardent leur phase à déclarer. Une phase pas encore vérifiée s'affiche « L1? » dans le tableau.
- Nouveau : **bouton « Soutenir le projet »** dans la barre du haut, lien direct vers la cagnotte. Le lien est modifiable
  depuis TED admin et se met à jour à chaque vérification de la licence (s'il change ou disparaît, le bouton suit).
- Nouveau : **on ne supprime plus ce que la régulation utilise encore**. Tant qu'un appareil est dans le tableau des
  onduleurs (piloté ou surveillé), TedEnergy refuse de supprimer l'appareil, de lui retirer une connexion, de retirer
  l'intégration d'un appareil MQTT ou d'un connecteur (cloud, API locale) dont l'une des valeurs lui sert, et d'effacer
  le compte d'un connecteur. Le message nomme les onduleurs concernés. Ordre à suivre : retirer l'appareil de la
  régulation (page Onduleurs), supprimer ensuite une connexion ou toutes, puis l'appareil. Désactiver une connexion ou
  en ajouter une reste possible à tout moment.
- Nouveau : **le rapport de configuration joint à la validation de la licence décrit toutes les connexions de chaque
  appareil** (format 3), pour établir la liste des onduleurs compatibles avec les connexions qui fonctionnent
  réellement. Pour chaque connexion : type (MQTT local, API locale, cloud, Home Assistant) et connecteur, position,
  activée / validée / préférée / utilisée, entités anonymisées, réglages, compteurs d'écritures (réussies, échouées,
  erreur 01, mesures illisibles), temps d'utilisation, pannes (nombre, durée totale, plus longue) et bascules. S'y
  ajoutent les réglages de bascule, les batteries déclarées (nombre, modèles, capacité), le résultat du test de
  validation, la phase et la priorité de décharge. Jamais de mesure, d'adresse, de jeton, d'identifiant d'appareil ni
  de nom saisi. La phrase de transparence de la carte Licence est mise à jour.
- Changé : dans la fiche d'un appareil, **« Ajouter une connexion » ne propose le cloud et l'API locale que pour les
  marques dont TedEnergy a développé le connecteur** (Zendure, EcoFlow, Indevolt, IsyPower). MQTT local et Home
  Assistant restent proposés pour toute marque : les entités se saisissent alors à la main (loupe de chaque champ).
- Nouveau : **un équipement peut être lu sous plusieurs formes de puissance** (page Équipements › fiche de l'équipement
  › « Forme de la puissance »). Demande d'un utilisateur : sa pompe à chaleur Hisense n'indique pas de puissance, seulement
  un compteur de consommation en kWh. Quatre formes en plus de la puissance en W ou kW : **compteur d'énergie** (Wh, kWh :
  la puissance est calculée à partir de la variation du compteur sur une fenêtre réglable de 10 minutes par défaut, et
  l'énergie comptée est celle du compteur lui-même ; la remise à zéro du compteur en fin de journée est gérée), **courant
  en ampères × tension** (monophasé ou triphasé), **marche / arrêt avec une puissance fixe** (prise, relais, thermostat :
  pour un thermostat, TED utilise l'action en cours quand elle existe). Par défaut (« Automatique »), la forme est reconnue
  d'après l'unité de chaque entité ; les équipements déjà déclarés ne changent pas. Une puissance calculée ou estimée
  s'affiche avec « ≈ ». Les formes s'appliquent à tous les types d'équipements, pas seulement aux pompes à chaleur.

- Nouveau : **Indevolt et IsyPower sont deux marques distinctes**, chacune avec son connecteur (Paramètres › Système ›
  Connecteurs d'appareils › Indevolt / IsyPower), sa liste de modèles et sa fiche dans la page Onduleurs. « Matter
  France » (simple revendeur) disparaît des textes. Indevolt : SolidFlex 1200 / 2000 / 3000 / 3000 Hybrid Pro / 3600,
  PowerFlex 2000 / 3000 / 3600, BK1600 et BK1600 Ultra. IsyPower : Titan 2400, avec ses modules d'extension LINK.
- Corrigé : **une IsyPower Titan 2400 restait bridée à 800 W en décharge**. Le connecteur appliquait lui-même la limite
  de la 1re génération d'Indevolt (charge 1 200 W, décharge 800 W) à un Titan qui se présente comme un BK1600 dans l'API.
  La puissance maximale vient maintenant du modèle et des modules LINK déclarés (Titan seule : 1 000 W en décharge,
  1 200 W en charge ; avec 1 LINK 2000 : 2 000 W / 2 400 W ; avec 2 LINK 2000 ou 1 LINK 4000 : 2 400 W), réglable dans
  **« Réglages de l'appareil »** du connecteur (modèle, modules LINK, plafonds de charge et de décharge). Le nombre de
  modules LINK ne se lit pas dans l'API : il se déclare. Si l'appareil **refuse** une puissance, TED envoie la limite
  garantie par Indevolt (800 W en décharge) au lieu de laisser une consigne sans effet ; s'il **ne tient pas** la
  puissance demandée, TED retient la puissance qu'il tient réellement pendant 1 h. Les appareils déjà ajoutés dans le
  connecteur Indevolt gardent leurs variables (`local_indevolt_…`) : pour une IsyPower, soit un plafond personnalisé,
  soit un nouvel ajout dans le connecteur IsyPower (variables `local_isypower_…`).
- Nouveau : **consigne Home Assistant ramenée à la borne de l'entité**. Une entité « number » de puissance (W, kW)
  qui annonce un maximum plus bas que la puissance de la fiche n'est plus appelée au-delà : TED envoie le maximum de
  l'entité et le dit dans le journal (`[BORNE_ENTITE]`), au lieu d'une valeur que Home Assistant refuse. Une entité en %
  (niveau de coupure pris pour une puissance) garde son refus, qui reste l'alerte.
- Nouveau : une fiche IsyPower reliée à une intégration de Home Assistant qui la voit comme un « Indevolt-BK1600 »
  (limites 800 W / 1 200 W) affiche un avertissement qui oriente vers le connecteur IsyPower.
- Changé : l'API locale Zendure n'est **plus « expérimentale »** (fiable à l'usage) ; EcoFlow, Indevolt et IsyPower le
  restent jusqu'à un essai sur un vrai appareil.

- Corrigé : **les batteries déclarées dans la fiche d'un appareil n'étaient pas enregistrées** (Paramètres › Appareils ›
  Modifier › Batteries). Elles avaient leur propre bouton « Enregistrer », que « Appliquer » ne déclenchait pas : la
  fenêtre se fermait et les batteries détectées, ajoutées ou retirées étaient perdues. Les deux boutons de l'onglet
  sont supprimés : **« Tester la connexion »** (bas de la fiche) lit aussi les batteries déclarées et explique ce qui
  ne va pas, **« Appliquer »** enregistre la connexion ET les batteries (bloqué tant qu'une batterie nouvelle ou
  modifiée n'est pas testée). Un appareil encore en brouillon peut aussi déclarer ses batteries ; supprimer un appareil
  supprime ses batteries. Le test d'une batterie affiche enfin le détail (entité introuvable…) au lieu de « Test
  impossible ».
- Nouveau : **prévision de la production solaire à 24 h / 48 h** (Paramètres › Mon installation › Prévision
  météorologique). TedEnergy lit gratuitement, sans compte, les prévisions météo heure par heure (Open-Meteo : meilleur
  modèle automatique, Météo-France, ECMWF, ICON, GFS ; MET Norway) et calcule la production **théorique** de vos
  panneaux (kWh et pic en kW pour aujourd'hui, demain et après-demain) d'après leur puissance, leur inclinaison, leur
  orientation et la position du soleil. Le rendement réel de l'installation est **appris chaque jour** sur votre
  production passée (ombrages, pertes, panneaux ajoutés ou retirés : repris en quelques jours, sans rien saisir). La
  page compare chaque prévision de la veille à la production réelle (écart mesuré par source). Nouvelles variables
  `meteo.*` pour les automatisations (dont `meteo.solar_next_day_kwh`, la prochaine journée à venir) et deux modèles
  d'automatisation : « Prévision solaire faible : charger la nuit précédente » et sa variante Tempo rouge. C'est une
  **estimation fondée sur des prévisions** : elle ne reflète jamais parfaitement la réalité et TedEnergy ne peut être
  tenu responsable d'une estimation erronée. Seules les coordonnées arrondies à 1 km sont envoyées aux services météo.
- Corrigé : **retirer un onduleur ne supprime plus l'appareil**. Sur la page Onduleurs, « Retirer de la régulation »
  (menu ⋮ ou fiche de l'onduleur) met l'onduleur à l'arrêt puis le fait redevenir un appareil « pas encore utilisé » :
  sa fiche, ses connexions et tous ses réglages restent dans Paramètres › Appareils, et « Ajouter un onduleur » le
  remet en service sans rien ressaisir. Seule la page Appareils supprime un appareil.
- Nouveau : un **appareil peut être enregistré sans validation** (« Enregistrer sans valider ») pour compléter sa
  saisie petit à petit. Chaque connexion garde son état de test : une connexion non validée est enregistrée mais
  **jamais utilisée** (ni par la régulation, ni par la page Onduleurs), sa pastille est barrée en rouge. Un appareil ne
  devient onduleur que s'il a au moins une connexion testée et validée.
- Nouveau : **un nouvel appareil n'a aucune connexion au départ** (plus de Home Assistant par défaut) : la fiche
  invite à choisir la première, parmi MQTT local, API locale, cloud ou Home Assistant.
- Nouveau : **erreur 01 en cycle** (Paramètres › Surveillance et sécurité). Les cinq réglages (délai, écart toléré,
  intervalle entre deux tests, puissance du test, priorité après déblocage) sont réunis dans la rubrique, avec deux
  courbes qui suivent les valeurs saisies : la détection (consigne, mesure, zone tolérée, fin du délai) et le cycle
  (mise à l'écart, tests, reprise, priorité), pour un onduleur bloqué ou qui répond et un test qui réussit au 1er…5e
  essai. Des avertissements signalent les réglages incohérents. La « priorité après déblocage » (durée minimale de
  travail d'un onduleur) quitte la rubrique Répartition ; sa valeur est conservée.
- Nouveau : **appareils et connexions**. Un appareil se déclare une seule fois (Paramètres › Appareils) avec toutes
  ses connexions : MQTT local, API locale du fabricant, cloud du fabricant, Home Assistant. Elles sont classées par
  priorité (par défaut dans cet ordre, réglable par appareil) ; TedEnergy utilise la première qui répond. Si elle
  n'a plus de mesure pendant 30 s (réglable), il passe sur la suivante et réécrit la consigne en cours ; il revient
  sur la préférée quand elle est stable depuis 5 min (réglable). Les fiches d'onduleur existantes deviennent des
  appareils avec leur connexion actuelle, rien à ressaisir ; les champs de la fiche restent ceux de la connexion
  préférée, donc un retour à la 3.7.9 garde chaque appareil fonctionnel (une seule connexion).
- Nouveau : la page **Onduleurs** ne règle plus que l'usage d'un appareil : rôle (piloté ou surveillé), phase,
  priorité de décharge, actif. « Ajouter un onduleur » choisit parmi les appareils déclarés et validés. Connexions,
  entités, puissances maximales, niveaux de batterie et protections se règlent dans Paramètres › Appareils.
- Nouveau : le **remplissage automatique** suit le type de la connexion affichée (cloud, API locale, MQTT local ou
  Home Assistant) : une seule liste d'appareils, celle de ce type, sans mélange avec les autres sources. Un appareil
  MQTT relié à un appareil du cloud est proposé tel quel : le choisir l'intègre et le sépare du cloud (après accord)
  pour en faire une vraie connexion MQTT local.
- Nouveau : **chaque connexion se teste séparément**, depuis sa carte, sans l'ouvrir. Le bouton n'apparaît que pour une
  connexion jamais testée, modifiée ou en échec ; une connexion déjà enregistrée et inchangée n'est pas à retester.
  « Tester toutes les connexions » est à côté d'« Appliquer ». Les problèmes d'une connexion en échec sont listés dans
  sa carte.
- Corrigé : un appareil dont la connexion préférée change (par exemple l'API locale ajoutée avant le cloud) n'était
  plus indiqué « utilisé par » dans la liste du cloud de son fabricant.
- Nouveau : **niveaux de batterie à glisser** (Paramètres › Batteries et puissances maximales). Une barre pour les
  niveaux généraux et une seule jauge pour tous les onduleurs pilotés : une flèche étiquetée par onduleur, à son
  niveau réel ; un clic sur la flèche ou sur son nom choisit l'onduleur dont on règle les deux poignées (souris,
  doigt ou flèches du clavier : niveau minimal pour décharger, niveau maximal pour charger), les petits traits
  montrent les niveaux des autres. Les flèches se rangent sur plusieurs lignes quand des batteries ont le même
  niveau. La même barre est dans la fiche de l'appareil (onglet Niveaux et protections).
- Nouveau : interrupteur **« Appliquer ces niveaux à tous les onduleurs pilotés »**, avec mémoire. Activé, chaque
  onduleur prend les niveaux généraux (écrits dans l'appareil quand sa fiche a les entités de niveau, contrôlés et
  corrigés à chaque actualisation) et ses niveaux d'origine sont gardés dans sa fiche ; désactivé, ils sont remis.
  Désactivé, un niveau changé dans l'application du fabricant est repris par TedEnergy, jamais écrasé. Un niveau
  imposé par une automatisation reste prioritaire (poignée verrouillée). Les deux niveaux généraux ne sont plus
  des champs de la liste des réglages : ils se règlent avec les barres. Interrupteur activé, la jauge des onduleurs
  disparaît : il ne reste que la liste des onduleurs concernés et, repliés, leurs niveaux d'origine gardés.
- Nouveau : **Diagnostic par type de connexion**. Quatre pages remplacent les anciennes (liaison Home Assistant,
  cloud Zendure, API locale Zendure) : Home Assistant, Cloud, API locale, MQTT. Chacune recense les appareils de
  toutes les marques et affiche leurs valeurs, l'heure du dernier relevé, l'intervalle moyen entre deux relevés,
  l'heure du dernier changement de valeur, les consignes envoyées et leur confirmation, le détail minute par minute
  de la dernière heure et les événements. La fiche d'un appareil indique si cette connexion est celle utilisée ou
  en secours. Le MQTT, qui n'avait aucune page de diagnostic, a la sienne.
- Déplacé : le **journal des onduleurs** et les téléchargements de journaux sont dans Notifications et journal
  (onglet Journal des onduleurs).
- Nouveau : **Sauvegarde et versions** réunies en une page à trois onglets (Sauvegardes, Versions, Maintenance). Le
  fichier de sauvegarde porte une **étiquette** (version de TedEnergy, format des données) ; à la restauration,
  TedEnergy la lit et dit, avant de remplacer quoi que ce soit, si la version en service peut la relire (refus expliqué
  si le format est plus récent). La configuration remplacée est gardée dans le dossier « sauvegardes » (les cinq
  dernières). Le détail des commandes Home Assistant (console) est dans l'onglet Maintenance.
- Nouveau : page **Communication avancée** (Paramètres › Appareils et connexions) : liaison en temps réel avec Home
  Assistant, actualisation des variables, relecture des consignes envoyées, délais du compteur Shelly. L'alerte de
  tension d'une batterie et les réglages de la puissance réelle des batteries sont dans Batteries et puissances maximales.
- Nouveau : les **batteries d'un appareil** se déclarent dans sa fiche (onglet Batteries, onduleurs pilotés ou
  surveillés), plus dans le Diagnostic, qui n'affiche que leurs valeurs.
- Nouveau : **valeurs stables d'un appareil** pour les automatisations : `appareil.<référence>.<valeur>` (niveau de
  batterie, puissances, état de calibration…, plus des valeurs ajoutées dans la fiche : prochaine calibration, injection
  sur le réseau…). La valeur lue est celle de la connexion utilisée par l'appareil : l'automatisation continue de
  fonctionner quand il change de connexion. Un effet peut aussi imposer une valeur d'appareil (cible « Une valeur d'un
  appareil »). Une automatisation liée à l'entité d'une seule connexion est signalée, avec la valeur stable à
  utiliser à la place. Une automatisation qui cite un appareil supprimé ou retiré d'un connecteur est désactivée,
  avec la raison.
- Corrigé : un niveau envoyé à un appareil est ramené dans la plage que son entité accepte (Zendure : minimum 5 à
  50 %, maximum 70 à 100 %) au lieu d'être refusé.
- Paramètres : rubriques réorganisées (Mon installation, Appareils et connexions, Fonctionnement, Réglages fins,
  Données, Accès et services, Interface et aide, Licence en dernier).

## 3.7.9

- Corrigé : **priorité de décharge « basse »**. Un onduleur de priorité basse
  pouvait prendre, à la fin de la durée minimale d'un autre, sa place dans la
  sélection parce que sa batterie était plus haute, puis être écarté sans être
  remplacé : un seul onduleur travaillait alors que « Un onduleur de plus tous
  les… » en demandait deux (vu le 25/10, J6 et 2400AC en priorité basse). Une
  priorité basse ne remplace plus jamais un autre onduleur ; elle n'est appelée
  qu'en dernier recours, quand les autres ne peuvent pas fournir la consigne
  (batteries au niveau minimum, ou tous à leur puissance maximale). Un onduleur
  de priorité haute ou moyenne remplace un onduleur de priorité basse déjà au
  travail. La priorité solaire reste la première.
- Nouveau : Paramètres › Répartition entre les onduleurs › **Intégrer les
  onduleurs de priorité basse** (désactivé par défaut) et « Écart de batterie
  pour l'intégrer » (25 points). Quand la batterie d'un onduleur de priorité
  basse dépasse de cet écart celle du meilleur onduleur de priorité élevée ou
  moyenne, il travaille avec les autres (batterie la plus haute d'abord), puis
  redevient un renfort quand l'écart retombe sous 80 % du seuil. Pour une
  batterie sans panneaux solaires, ou une voiture (V2G) dont on garde une réserve.
- Nouveau : **retour à une version précédente depuis l'interface**, en gardant
  vos réglages. Paramètres › Système › **Version** affiche la version en
  service et, à partir de la version 3.7.10, les deux versions précédentes
  contenues dans l'image (rien à télécharger, que ce soit sous Home Assistant ou
  Docker). Avant le changement, TedEnergy montre ce que vous perdez (nouveautés
  des versions concernées, réglages, fiches d'onduleurs ou automatisations que la
  version précédente ne connaît pas), prend un point de restauration de la
  configuration (bouton « Annuler le changement ») puis redémarre. Une version
  qui ne démarre pas est abandonnée toute seule au profit de la version
  installée. Une version précédente dont les données ne seraient plus lisibles
  (nouveau format de données, autre version de Python…) est affichée en grisé avec
  la raison. Une pastille « Version précédente » reste visible tant qu'elle est en
  service ; une mise à jour de l'application ou de l'image rétablit la version la
  plus récente. La version 3.7.9 elle-même ne contient pas encore de version
  précédente à proposer : elles apparaissent à partir de la 3.7.10.
- Une version ne supprime plus ce qu'elle ne comprend pas : les champs d'une
  automatisation, d'une règle de notification ou d'un équipement ajoutés par une
  version plus récente sont conservés à l'enregistrement (les fiches d'onduleurs
  le faisaient déjà), pour qu'un retour en arrière ne fasse rien perdre.

- Marge de redémarrage dans les deux sens : le réglage « Marge de redémarrage »
  (2 points par défaut, et la marge de la fiche de chaque onduleur) ne retenait
  que la charge d'une batterie presque pleine. Elle retient maintenant aussi la
  décharge d'une batterie presque vide : avec un minimum de 10 % et 2 points,
  une décharge ne démarre qu'à partir de 12 % (au lieu de 11 %, d'où le va-et-vient
  autour du minimum) ; une décharge déjà commencée continue jusqu'au minimum.
  La charge, elle, reste possible dès le minimum. Pas de marge pour un onduleur en
  priorité solaire (son solaire alimente le réseau sans vider la batterie), pour un
  minimum imposé par une automatisation ni pour la voiture d'une borne V2G. Un seul
  réglage vaut pour les deux sens ; la valeur déjà enregistrée est conservée.

- Corrigé (régression de la 3.7.8) : un onduleur « en veille » pouvait
  continuer de décharger pendant des heures. La consigne de 0 W n'était plus
  renvoyée une fois écrite, alors que le cloud du fabricant peut perdre une
  écriture (relue « 0 W » par simple écho) : l'onduleur gardait sa dernière
  puissance jusqu'au minimum de sa batterie et, en mode sortie, le petit
  solaire ne chargeait plus. TedEnergy contrôle désormais le 0 W appliqué (valeur
  relue après 25 s et, en veille, décharge mesurée de la batterie) et le
  réécrit en silence, 3 essais. Sans effet, l'onduleur est laissé de côté
  (même pause que « consigne changée par un autre système », 5 min par défaut)
  avec un message distinct : « consigne non appliquée par l'onduleur » (écriture
  perdue ou refusée, ou autre système qui pilote l'onduleur). Une charge lancée
  par le fabricant n'est jamais contrariée.
- Page Onduleurs : la pastille d'un incident terminé (« Autre système 16:23 →
  rétabli 16:26 », « Consigne non appliquée → reprise ») porte un ✕ pour
  l'effacer sans attendre l'heure de rappel. Une alerte encore en cours ne peut
  pas être effacée : seul un incident terminé a ce bouton.
- Quand le sélecteur de mode change de sens (charge / décharge), la consigne
  de ce sens est réécrite même si elle semblait déjà en place.
- Fiches des onduleurs : contrôle d'ensemble, pour toutes les marques et
  toutes les connexions (Home Assistant, cloud, API locale, MQTT), au bouton
  « Tester », à l'enregistrement et sur la page Onduleurs (pastille « fiche à
  corriger » / « fiche à vérifier » pour les fiches déjà enregistrées).
  Refusé : une action Home Assistant qui n'existe pas (intégration absente),
  une action qui vise un appareil d'un connecteur de TedEnergy, une entité de
  commande ou un niveau de batterie déjà utilisé par un autre onduleur.
  Signalé sans bloquer : une mesure partagée, une entité hors de l'appareil
  choisi, des consignes et un niveau de batterie lus par des sources
  différentes.
- Indevolt / IsyPower : la fiche ne propose plus les actions
  indevolt.charge / indevolt.discharge si l'intégration officielle Indevolt
  n'est pas dans Home Assistant (IsyPower relié par une autre intégration
  recevait « 400: Bad Request ») : elle passe en deux consignes et oriente vers
  le connecteur de TedEnergy. Le niveau visé en décharge n'est jamais sous le
  niveau minimal réglé dans l'onduleur. Message clair quand Home Assistant ne
  connaît pas une action.

## 3.7.8

- Charge standard sur surplus : « Export qui déclenche la charge » réglable de
  30 à 400 W, 100 W par défaut. En dessous de 100 W, un avertissement s'affiche
  et une fenêtre demande de confirmer : le seuil de sécurité est alors
  insuffisant pour une régulation fine. Les valeurs déjà enregistrées sont
  gardées (au-delà de 400 W : ramenées à 400 W).
- Corrigé : en mode Autonome sans rien à faire (batteries au minimum, par
  exemple au petit matin), la régulation renvoyait à chaque cycle la charge puis
  la décharge à 0 W : le sélecteur de mode des onduleurs passait de « input » à
  « output » deux fois par cycle (plus de 40 000 changements en 4 heures pour
  deux Hyper 2000 reliés par le cloud Zendure, qui ont ensuite cessé de
  transmettre leurs mesures). Désormais, un onduleur mis en veille passe une
  seule fois en décharge à 0 W (consigne de charge remise à 0 sans toucher au
  sélecteur), puis la régulation n'y touche plus tant que la veille dure.
- Une consigne de 0 W déjà appliquée n'est plus renvoyée toutes les 60 s : une
  charge lancée depuis l'application du fabricant (protection de la batterie,
  charge manuelle) n'est plus contrariée. L'onduleur est repris dès que la
  régulation en a besoin et que son niveau de batterie le permet.
- Nouveau : consigne d'un onduleur changée par un autre système (application du
  fabricant, charge de protection de la batterie, autre HEMS). La régulation
  compare chaque cycle les consignes relues à celles qu'elle a écrites ; si une
  autre valeur apparaît, l'onduleur est laissé de côté (plus aucune consigne,
  la valeur de l'autre système est conservée) : ligne orange et bouton ⚠ sur la
  page Onduleurs avec l'heure de l'incident et l'heure de reprise prévue,
  avertissement dans « Avertissements et erreurs » (« un seul HEMS doit piloter
  cet onduleur »). Il est repris 5 minutes après le dernier changement (réglage
  « Pause après une consigne changée par un autre système », 0 = pas de
  détection), ou tout de suite par le menu ⋮ « Reprendre la main » ; l'incident
  reste rappelé une heure (« Autre système 14:55 → rétabli 15:00 »). Un
  onduleur désactivé n'est pas surveillé.
- Une page de l'interface restée ouverte pendant une mise à jour de TedEnergy
  affiche « Une nouvelle version de l'interface est installée » avec un bouton
  « Recharger » (sinon elle gardait l'ancien affichage).
- « Niveau de batterie illisible » devient « Mesures non reçues (niveau de
  batterie inconnu) », et « Hors ligne » devient « Mesures non reçues » dans la
  page du cloud Zendure : l'appareil peut être en ligne dans l'application du
  fabricant sans que ses mesures arrivent.

## 3.7.7

- Corrigé : fausse erreur 01 « démarrage en charge non abouti » d'un onduleur
  dont la batterie presque pleine chargeait déjà avec tout son solaire et ne
  pouvait donc pas prendre de charge sur le réseau. La fin du démarrage applique
  désormais les mêmes exemptions que le contrôle habituel (limite de la
  batterie, batterie presque pleine) ; la charge solaire devient la limite de
  la batterie, sans consigne réseau superflue. Une consigne inférieure à 25 W ne
  lance plus de démarrage (il ne pouvait jamais aboutir).
- Délai avant l'erreur 01 : 240 s par défaut au lieu de 60 s (moins de
  fausses erreurs 01 sur les onduleurs lents). Une valeur déjà enregistrée dans
  Paramètres est gardée.
- Connecteur Indevolt / IsyPower (Paramètres › Connecteurs
  d'appareils) : même API locale chez les deux marques, nom et textes mis à
  jour. « Rechercher sur le réseau » parcourt aussi le réseau local (port 8080,
  appareil reconnu par son numéro de série) : il trouve désormais l'appareil
  quand TedEnergy tourne dans Docker ou en application Home Assistant, où la
  diffusion ne passait pas. L'ajout par adresse IP reste possible.
- Nouveau : test de validation des onduleurs (Diagnostic › Test des
  onduleurs). Toutes marques : lecture de chaque entité de la fiche (niveau,
  mesures, consignes et valeurs qu'elles acceptent, sélecteur et ses options,
  appareil Home Assistant commun), puis charge et décharge à faible puissance
  (100 W par défaut, de 50 à 200 W) avec retours à 0 W. Délai large par étape
  (180 s par défaut), l'onduleur testé est hors régulation pendant le test
  (ni erreur 01 ni démarrage suivi), les autres continuent. Résultat gardé avec
  sa date ; une fiche modifiée depuis redemande le test.
- Un nouvel onduleur piloté est enregistré désactivé. À son activation, s'il
  n'a jamais passé le test, TedEnergy propose de le lancer (« Non » l'active
  tout de suite).
- Sécurité : si Home Assistant refuse une consigne de puissance et que la
  fiche a une option d'arrêt du sélecteur (ex. « stop »), l'onduleur est mis à
  l'arrêt au lieu de rester dans le sens demandé avec la puissance gardée en
  mémoire (une batterie a injecté 2,2 kW de cette façon). Le refus est affiché
  en clair sur l'onduleur (ex. « n'accepte que 12 à 30 »), sans erreur 01.
  Rien ne change pour une fiche sans option d'arrêt ni pour une consigne
  simplement relue en retard.
- Sécurité : un onduleur supprimé, passé en surveillé ou dont les entités de
  commande changent est d'abord mis à l'arrêt (0 W), au lieu de garder sa
  dernière consigne.
- Test de la fiche : une consigne qui refuse 0 W, ou en % pour une fiche en W
  (par exemple une limite de niveau de batterie « cutoff capacity »), est
  refusée ; la plage acceptée par chaque consigne est affichée ; avertissements
  si la puissance maximale dépasse ce que l'entité accepte, s'il manque
  l'option d'arrêt du sélecteur, ou si une production solaire est renseignée
  sur un modèle sans entrée pour panneaux. Options du sélecteur comparées sans
  tenir compte des majuscules.
- Avertissement « sens de la puissance inversé ? » quand la batterie mesure
  l'inverse exact de sa consigne pendant plus d'une minute.
- Identification de phase : une consigne de test refusée n'enregistre plus de
  phase, et une batterie qui fournit bien plus que demandé est signalée.
- Marstek : sens de « Battery power » proposé « positive en charge »,
  option d'arrêt « stop » conseillée ; Venus E, E v3 et C sans entrée solaire.

## 3.7.6

- Correction : deux onduleurs ne peuvent plus porter le même « Identifiant
  interne ». Avant, une nouvelle fiche (ou un renommage) avec un identifiant
  déjà pris remplaçait en silence l'onduleur existant, sa phase comprise : en
  triphasé, une batterie « disparaissait » d'une phase à chaque ajout. Le test
  et l'enregistrement refusent maintenant l'identifiant pris, en nommant
  l'onduleur qui l'utilise.
- Sélecteur de mode : une option laissée sur « automatique » qui ne correspond
  à aucune option du sélecteur est refusée au test de la fiche (par exemple le
  sélecteur « User work mode » des Marstek : manual / anti-feed / ai). En marche,
  un sélecteur en échec est affiché sur l'onduleur (« Sélecteur ») et sur le
  tableau de bord, avec les options disponibles ; nouvel essai toutes les
  minutes au lieu de deux fois par cycle.
- Nouvelle alerte « Consignes sans effet » (tableau de bord, notification,
  téléphone) : en décharge Autonome, la consigne reste au maximum alors que le
  compteur reste à l'objectif, ou elle monte sans que le compteur change. Les
  onduleurs n'appliquent alors pas les consignes (sélecteur de mode, pilotage
  désactivé dans l'onduleur, régulation propre du fabricant) : l'alerte liste
  les onduleurs sans mesure de puissance et ceux dont le sélecteur échoue.
- Marstek : le sélecteur de sens forcé (« Force mode » / « Forcible
  charge/discharge ») est obligatoire dans la fiche ; prérequis « RS485 control
  mode » rappelé, passerelles ESPHome mentionnées.

## 3.7.5

- Correction : Zendure relié en MQTT local (SolarFlow 2400 AC, 2400 AC+ et
  autres modèles récents). Les textes publiés par l'appareil (« Output mode »,
  « Input mode », « Allow backflow », état de calibration « idle », état
  « standby », températures en kelvins) sont maintenant traduits comme les
  valeurs du cloud. Avant : calibration vue « en cours » alors que l'appareil
  était normal, changement de mode refusé (« Option inconnue ») donc aucune
  charge possible, et date de prochaine calibration recalculée en continu.
  Les batteries de l'appareil sont aussi lues en local, et les consignes de
  puissance passent l'appareil en smartMode (pas d'écriture en mémoire flash à
  chaque consigne, comme l'intégration Zendure).
- Borne de recharge pilotée : nouvelle case « Piloter la recharge » dans la
  fiche de la borne (page Équipements). Avec une entité de consigne de Home
  Assistant (courant en A, ou puissance en W / kW) et, si possible, un
  interrupteur marche / arrêt, TedEnergy décide quand et à quelle puissance la
  voiture charge, selon des objectifs cochés dans la fiche : niveau de secours
  (sous X %, charge tout de suite), prêt pour le départ (X % à une heure
  donnée, aux heures avantageuses puis à toute heure si le temps manque),
  heures avantageuses (périodes de l'abonnement ou plages fixes, jusqu'à X %)
  et surplus solaire (jusqu'à X %, surplus minimal, délais de démarrage et
  d'arrêt, voiture prioritaire ou non sur les batteries de la maison). Courant
  minimal et maximal de la borne, délai entre deux changements et durée
  minimale de marche / d'arrêt pour ménager le chargeur ; charge sur le réseau
  limitée par la puissance souscrite. Menu ⋮ : « Charger maintenant » et
  « Pause » jusqu'au débranchement. Tout se règle dans la fiche de la borne,
  rien dans la page Paramètres.
- Batteries de la maison : nouvelle case « Ne pas décharger les batteries de
  la maison pour la voiture » (cochée par défaut, toutes les bornes, même
  seulement mesurées) : quand la voiture charge (la nuit en heures creuses,
  par exemple), sa puissance est retirée de la mesure vue par la régulation et
  les batteries n'alimentent plus que la maison.
- V2G (expérimental) : « Utiliser la voiture comme une batterie pour la
  maison » crée un onduleur virtuel dans la page Onduleurs (réglé dans la fiche
  de la borne, désactivable, ni surveillé, ni cluster, ni zone). Sa priorité de
  décharge est basse : la voiture n'alimente la maison que lorsque les autres
  batteries ne suffisent plus. Niveau minimal de la voiture d'au moins 30 %,
  puissances minimale et maximale de restitution. Fonction non testée faute de
  matériel.
- Onduleurs : nouvelle « Priorité de décharge » dans la fiche (basse, moyenne
  par défaut, élevée). Ordre : priorité solaire, élevée, moyenne, puis basse ;
  une batterie en priorité basse n'est utilisée que si les autres ne suffisent
  pas et ne prend que le reste.

## 3.7.4

- Automatisations : dossiers. Bouton « Nouveau dossier » ; pour ranger une
  automatisation, maintenez-la (ou sa poignée) puis glissez-la sur un dossier,
  ou choisissez son dossier dans sa fenêtre. Un dossier se replie, se renomme,
  se déplace ; le supprimer remet ses automatisations hors dossier (aucune
  n'est supprimée). Les flèches ↑ ↓ disparaissent : l'ordre de la liste ne
  sert plus qu'à l'affichage.
- Automatisations concurrentes : quand deux automatisations imposent une
  valeur différente à la même cible (réglage, SOC minimum ou maximum d'un
  onduleur, entité de Home Assistant), un avertissement s'affiche sur chacune
  avec un lien vers l'autre, et vous choisissez leur priorité (1 = celle qui
  l'emporte quand elles sont actives en même temps ; 1, 2, 3… s'il y en a
  plus). La priorité disparaît d'elle-même quand elles ne sont plus
  concurrentes. Les configurations existantes gardent leur comportement
  (ordre de la liste) jusqu'au prochain enregistrement, qui fixe les priorités.
- Automatisations : nouvelle case « Notification téléphone » : chaque début et
  fin est aussi envoyé à l'application Android (avec la case du journal des
  notifications).
- Licence réduite (fin d'une période ACCESS, par exemple) : les onduleurs
  pilotés et les automatisations au-delà de la nouvelle limite sont désormais
  désactivés pour de bon (les derniers de la liste), au lieu d'être ignorés en
  silence. Un bandeau, l'historique des réglages et une notification envoyée
  aussi au téléphone disent lesquels ; à vous de choisir ceux à garder ou
  d'augmenter la licence. L'avertissement « fin de période » annonce à
  l'avance combien seront désactivés. Toutes les notifications de la licence
  vont désormais aussi au téléphone.
- Prix de l'abonnement : nouvelle rubrique « Abonnement » dans Paramètres ›
  Abonnement et tarifs, avec le prix TTC par mois ou par jour (bascule
  Mois / Jour). Il est ajouté chaque jour au coût du réseau du bilan du
  tableau de bord et des rapports PDF (un prix mensuel est réparti sur les
  jours du mois), et la facture nette en tient compte. Non renseigné (0 par
  défaut), il n'est pas compté.
- Le logiciel s'appelle désormais **TedEnergy** (anciennement TED Energy
  Controller) : nom de l'application, barre du haut et titre de l'interface,
  entrée de la barre latérale de Home Assistant, rapports PDF. Rien ne change
  pour l'installation : même application, mêmes réglages, mêmes entités.
- Nouvelle rubrique Paramètres › Connexion MQTT (expérimental) : le serveur MQTT
  intégré y a désormais sa place (onglet « Serveur intégré »), avec la liste des
  appareils connectés et un journal des derniers messages reçus.
- Appareils au format MQTT de Home Assistant (Zendure en « Contrôle MQTT » sur
  les modèles récents, OpenDTU, Shelly, Tasmota, ESPHome…) : leurs valeurs sont
  regroupées par appareil (une ligne par appareil, au lieu d'un « appareil » par
  valeur), lues et utilisables dans les fiches d'onduleur et les automatisations
  (variables mqtt_<appareil>_…), consignes comprises. Correction : un SolarFlow
  2400 AC+ apparaissait comme une trentaine d'appareils « Modèle non reconnu »
  dans Cloud onduleur, sans aucune valeur lue.
- Appareil Zendure présent à la fois dans le cloud et en local : relié
  automatiquement (même numéro). Ses valeurs alimentent les variables du cloud
  (fiches inchangées) et ses consignes partent en local tant qu'il est connecté,
  par le cloud sinon. Liaison retirable (« Séparer »).
- Vos propres appareils : créez un appareil, rangez-y les valeurs de votre choix,
  regroupez deux appareils, ignorez ceux qui ne servent pas.
- Valeurs d'un appareil (cloud ou MQTT) dans une fenêtre : recherche, filtre,
  valeurs en direct, identifiant à copier, fiches qui les utilisent. Cloud
  onduleur : liste compacte, familles repliables, recherche.
- Docker : le port MQTT (1885) est publié automatiquement par les scripts
  d'installation et de déploiement, avec un port libre proposé s'il est pris.
- « Cloud onduleur » devient **Connecteurs d'appareils** (Paramètres › Système) :
  l'entrée se déplie dans la barre latérale avec une page par marque, qui dit si
  la liaison passe par le **cloud du fabricant** ou par l'**API locale
  officielle** de l'appareil (sur votre réseau, sans Internet), ou pourquoi la
  marque n'est pas reliée et comment la relier autrement.
- Cloud Zendure : n'est plus expérimental (testé et validé sur de vrais appareils).
- Nouveau (expérimental) : **Zendure par l'API locale officielle** (SolarFlow
  800, 800 Plus, 800 Pro, 1600 AC+, 2400 AC, 2400 AC+, 2400 Pro), sans Internet ni
  compte : la connexion la plus rapide (interrogation toutes les 2 s), en plus du
  cloud. La page Zendure aide à choisir (API locale ou MQTT local d'abord, cloud
  pour les autres modèles) ; chaque connexion a ses propres variables
  (`local_zendure_…`, `mqtt_…`, `cloud_zendure_…`) et une fiche passe de l'une à
  l'autre en choisissant l'appareil dans son remplissage automatique. Un appareil
  du cloud qui a l'API locale s'y ajoute en un clic ; la page signale les fiches
  qui lisent encore par le cloud un appareil disponible en local.
- Configuration des onduleurs jointe à la validation de licence : type de
  connexion de chaque onduleur (API locale, MQTT, cloud ou Home Assistant), sans
  aucun nom ni adresse, pour la page Compatibilité du site.
- Nouveau (expérimental) : **cloud EcoFlow**, par la plateforme développeurs
  officielle (clé d'accès et clé secrète) : mesures en continu, consigne de sortie
  du PowerStream (pilotable), niveaux et réglages des DELTA 2 / RIVER 2 et STREAM.
- Nouveau (expérimental) : **Indevolt par l'API locale officielle** (la même que
  l'intégration Indevolt de Home Assistant), sans Internet ni Home Assistant :
  appareil ajouté par son adresse IP, mesures toutes les 5 s, charge et décharge
  commandées en temps réel, batteries détaillées.
- Fiche d'onduleur : en choisissant un appareil EcoFlow ou Indevolt d'un
  connecteur, ses champs sont remplis automatiquement (consignes, niveau,
  puissances, limites).
- Marstek et Anker SOLIX étudiés, non reliés pour l'instant : le cloud Marstek
  n'est pas ouvert et son API locale ne peut être interrogée assez souvent ; le
  cloud Anker ne donne qu'une mesure par minute. Leur page explique comment les
  relier autrement (MQTT, Home Assistant). Batteries Marstek publiées par la
  passerelle hm2mqtt : valeurs bien nommées dans Connexion MQTT.

## 3.7.3

- Tableau de bord : le flux d'énergie en direct a deux vues, « Maison » (par
  défaut, maison animée : maison, panneaux, réseau, batteries, et selon vos
  équipements garage et voitures, pompe à chaleur, piscine) et « Blocs »
  (l'affichage d'avant, avec les équipements reliés au bloc Maison). Le choix
  est mémorisé dans le navigateur.
- Équipements : nouveaux types (ballon d'eau chaude, pompe à chaleur,
  piscine, électroménager, chauffage). Pour chaque équipement : « Afficher sur
  le flux d'énergie », « Inclure dans les rapports PDF » (cochés par défaut),
  seuil d'affichage sur la maison animée (en dessous, il n'apparaît pas :
  congélateur au repos…) et « Déjà compté dans un autre équipement ».
- Borne de recharge : entités facultatives « voiture branchée » et « niveau de
  la voiture », borne bidirectionnelle (la voiture peut alimenter la maison,
  affichage seulement). 1 ou 2 bornes : une voiture dessinée par borne ; 3 ou
  plus : une station qui les réunit, détail de chaque borne au clic.
- Bilan des équipements (page Équipements et rapports PDF) : énergie, part de la
  maison, origine estimée (solaire, batteries, voiture, réseau) minute par
  minute, coût du réseau au prix de chaque période, durée de marche, pic,
  recharges des véhicules et reste de la maison non identifié.
- Flux d'énergie, vue « Maison » : la maison est désormais en 3D, comme une
  maquette d'architecte éclairée en vrai (soleil selon l'heure et la saison,
  ombres, nuit avec fenêtres et lampadaire allumés). Chaque liaison est une
  traînée lumineuse continue, la puissance de chaque objet est affichée à côté
  de lui, et on peut faire tourner la vue en la faisant glisser. Pompe à
  chaleur, ballon d'eau chaude et piscine n'apparaissent que s'ils sont
  déclarés ; l'électroménager, le chauffage et les prises sont listés sous
  « Maison ». L'ancienne maison animée en 2D est retirée. Sans 3D sur
  l'appareil (navigateur ancien, accélération graphique coupée), la vue
  « Blocs » s'affiche automatiquement. La bibliothèque 3D (three.js, licence
  MIT) est fournie avec TED : rien n'est chargé depuis Internet.
- Un équipement peut additionner plusieurs entités de puissance (groupe de
  radiateurs, piscine : pompe de filtration + chauffage de l'eau…).
- Borne de recharge : capacité de la batterie de la voiture et niveau visé
  (facultatifs) ; avec le niveau de la voiture, la vue 3D éclaire sa batterie
  dans le plancher et affiche le temps de recharge restant estimé
  (variable d'automatisation equipment.<clé>.charge_eta_min).
- Application Android : la borne de recharge s'affiche sur l'accueil (garage et
  voiture), sans mise à jour de l'application.
- Automatisations et notifications : chaque règle s'ouvre dans sa propre
  fenêtre, et « Enregistrer » n'enregistre que celle-là. Une automatisation
  devenue invalide (entité disparue, onduleur supprimé…) n'empêche plus d'en
  corriger une autre (« Certaines automatisations sont incomplètes »).
  Fermer la fenêtre sans enregistrer demande confirmation ; l'ordre (flèches
  ↑ ↓) et la suppression sont enregistrés tout de suite.
- Cloud Zendure : la « prochaine calibration » n'est plus « indisponible »
  après le passage de l'intégration Zendure de Home Assistant au cloud. La date
  qu'affichait l'intégration est reprise de l'historique de Home Assistant
  (10 derniers jours), et une batterie pleine fixe la date à maintenant
  + 30 jours. Les automatisations de calibration fonctionnent de nouveau.
- Paramètres › Cloud onduleur : la page ne saute plus pendant l'actualisation
  des valeurs (mise à jour en place, volets ouverts conservés).
- Valeurs de TED dans Home Assistant (lecture seule) : avec le cloud Zendure,
  l'intégration Zendure de Home Assistant ne peut plus tourner en même temps
  (une seule connexion par compte). TED rend donc toutes ses valeurs à Home
  Assistant : celles des appareils du cloud (niveaux, puissances, batteries,
  consignes, prochaine calibration…) et celles de la régulation (mode,
  consignes, compteur, onduleurs, équipements). Home Assistant ne peut rien
  modifier.
  - Publication MQTT (Paramètres › Valeurs pour Home Assistant) : TED se
    connecte au serveur MQTT de Home Assistant (application Mosquitto broker,
    trouvée automatiquement dans l'application Home Assistant de TED) ; les
    entités apparaissent toutes seules, rangées par appareil, avec les mêmes
    noms que l'intégration Zendure (sensor.b1_electric_level…) pour que les
    scripts et scènes existants remarchent sans retouche ; valeurs de la
    régulation : sensor.ted_grid_power_w, binary_sensor.ted_tarif_off_peak…
  - API locale, sans identifiant, depuis le réseau local seulement :
    /api/variables (toutes les valeurs) et /api/variables/<identifiant>.
  - Aide › « Utiliser les valeurs de TED dans Home Assistant » : mode
    d'emploi, adresses, liste des valeurs en direct et configuration YAML
    (intégration rest) générée en cochant les valeurs voulues.

## 3.7.2

- Automatisations : les variables du cloud Zendure ont leur propre catégorie
  dans le choix des valeurs (« Une entité du cloud Zendure », rangées par
  appareil : onduleur puis ses batteries), à côté de « Une entité de Home
  Assistant ». Même chose pour la cible d'un effet « valeur imposée ». Un
  futur cloud d'une autre marque aura automatiquement sa catégorie.
- Cloud Zendure : les réglages modifiables de l'onduleur deviennent des listes
  de choix et des interrupteurs, avec les mêmes noms et options que
  l'intégration Zendure de Home Assistant : contournement vers le réseau
  (`select.cloud_zendure_<x>_grid_reverse` : disabled / allow / forbidden),
  sortie hors réseau, passage direct, chauffage automatique, ventilateur,
  signal sonore, voyant… Une automatisation peut leur imposer une valeur et
  la remettre à la fin.
- Cloud Zendure : nouvelle variable « Prochaine calibration »
  (`sensor.cloud_zendure_<x>_next_calibration`), calculée comme le faisait
  l'intégration Home Assistant (30 jours après la fin d'une calibration ou
  après une charge à 100 %) et gardée après un redémarrage. Le modèle
  « Auto-calibration : batterie pleine avant » la trouve tout seul.
- Puissance réelle des batteries : la limite repérée est toujours une
  puissance de la batterie, jamais une consigne. Le solaire des panneaux de
  l'onduleur entre aussi dans la batterie : la consigne possible est
  recalculée à chaque mesure avec le solaire du moment et remonte aussitôt
  quand un nuage fait baisser le solaire (avant : 600 W par minute). La page
  Onduleurs affiche la limite de la batterie et la consigne qui s'en déduit.
- Erreur 01 et batterie limitée ne se confondent plus : l'écart de la
  puissance réelle des batteries se mesure comme celui de l'erreur 01, côté
  réseau (prise au réseau en charge, sortie AC en décharge), donc les pertes de
  conversion ne passent plus pour une limite. Sans ces mesures, la comparaison
  se fait sur la batterie avec une part de pertes (8 %) ajoutée aux deux
  écarts. Un onduleur qui applique sa consigne à l'écart de l'erreur 01 près
  n'est jamais en erreur 01.
  Les Paramètres préviennent quand l'écart toléré ou la durée de la puissance
  réelle des batteries n'est pas plus petit que celui de l'erreur 01 (à
  corriger d'un côté ou de l'autre). Nouveaux défauts : 50 W et 30 s.
- Nouveau (expérimental) : « Cloud onduleur » (Paramètres › Système). TED se
  relie directement au cloud Zendure avec le jeton de l'application Zendure,
  sans passer par Home Assistant : liste des appareils du compte, choix des
  appareils à intégrer, puis déclaration habituelle dans la page Onduleurs
  (surveillé ou piloté). Chaque champ d'une fiche accepte une entité de
  Home Assistant ou une variable du cloud (ex.
  `number.cloud_zendure_j1_output_limit`) ; la loupe indique la source et
  peut filtrer Home Assistant / Cloud Zendure ; le remplissage automatique
  Zendure propose les deux sources. Les deux connexions coexistent. Mesures
  reçues en continu (liaison MQTT sortante, aucun port à ouvrir) ; tableau
  des variables en temps réel avec l'âge de chaque valeur, le rythme de ses
  mises à jour et le délai de confirmation des consignes ; relecture complète
  réglable. Mêmes consignes que par Home Assistant (limites de sortie et
  d'entrée, mode AC, niveaux de batterie). Autres marques à l'étude (EcoFlow,
  Anker SOLIX, Marstek…). Fonction incluse dans la licence (droit
  « cloud Zendure », actif par défaut). Batteries détaillées (niveau,
  puissance, état, tensions, delta, température, intensité, santé),
  rattachées à leur onduleur comme avec Home Assistant (page Diagnostic).
  Le cloud Zendure n'envoie les mesures qu'à une seule connexion par compte :
  TED ou l'intégration Zendure de Home Assistant (en cas de conflit, TED le
  signale et fait une pause de 10 minutes pour ne pas la perturber).
- Correction : au redémarrage de TED (ou de Home Assistant), les onduleurs
  n'apparaissent plus en « Auto-calibration » le temps que leurs mesures
  arrivent. Un état encore inconnu (« unavailable ») n'est plus pris pour une
  calibration : ni mise en veille forcée, ni écran rouge pendant une minute.
  Pendant les 2 premières minutes, l'onduleur est affiché « En attente des
  premières mesures » (bandeau sur le tableau de bord) et la régulation le
  reprend dès que ses mesures arrivent. Après une vraie auto-calibration,
  l'onduleur repart en 10 secondes au plus (au lieu d'une minute).
- Diagnostic : nouvelle rubrique « Cloud Zendure » (une par cloud configuré) :
  état de la liaison, messages reçus minute par minute sur la dernière heure,
  régularité des relevés de chaque appareil (écart habituel et plus long écart
  entre deux relevés, âge et rythme de chaque mesure), fiches qui utilisent ses
  variables, consignes envoyées avec leur résultat (confirmée et en combien de
  temps, sans réponse, non envoyée), événements de la liaison.
- Diagnostic › Batteries : « Détecter dans le cloud Zendure » en plus de
  « Détecter dans Home Assistant » (le bouton de la source utilisée par la
  fiche de l'onduleur vient en premier ; un onduleur déclaré avec Home
  Assistant est retrouvé dans le cloud par son nom). Capacité des batteries
  du cloud remplie, nombre de batteries annoncé lu aussi dans le cloud.
- Nouveau (expérimental) : serveur MQTT intégré à TED, dans la même rubrique.
  Les appareils Zendure qui proposent « Contrôle MQTT » dans l'application
  Zendure s'y connectent directement sur le réseau local, sans Internet ni
  serveur Mosquitto à installer : un interrupteur, puis TED affiche l'adresse
  IP, le port (1885 par défaut), l'utilisateur et le mot de passe à saisir
  dans l'application, avec un test de connexion et la liste des appareils
  connectés. Mêmes variables que le cloud : les fiches d'onduleur ne changent
  pas, les consignes partent en local dès que l'appareil y est connecté.
  Mise en service guidée en 5 étapes vérifiées : serveur démarré, port ouvert
  (application Home Assistant : bouton « Ouvrir le port et redémarrer TED »,
  port libre proposé ; Docker / Synology : commande exacte), test de connexion
  par l'adresse et le port que l'appareil utilisera, valeurs à saisir dans
  l'application Zendure, appareil connecté.
- Lectures dans Home Assistant beaucoup plus légères : avant, chaque valeur
  était lue par sa propre requête, et souvent plusieurs fois par cycle
  (environ 240 requêtes par cycle avec 8 onduleurs). Désormais une liaison
  en temps réel (API WebSocket de Home Assistant) : Home Assistant envoie
  lui-même chaque changement des seules entités utilisées par la
  régulation, sans aucune requête de lecture. Si la liaison est coupée,
  toutes les valeurs d'un cycle sont lues en une seule requête (lecture
  groupée). Les consignes restent envoyées et vérifiées une par une. La
  régulation réagit plus vite (le cycle n'attend plus ces lectures).
  Réglage avancé « Liaison en temps réel avec Home Assistant » (Paramètres
  › Connexion, activé par défaut) ; état de la liaison et nombre de
  requêtes : page Diagnostic › Liaison avec Home Assistant.
- Régulation plus rapide : la configuration n'est plus relue sur le disque
  environ 200 fois par cycle (calcul d'un cycle environ 3 fois plus court),
  et le compteur Shelly Pro 3EM est lu par une demande courte (seulement la
  mesure : ~40 ms au lieu de ~200 ms). La lecture complète du Shelly reste
  faite au démarrage, toutes les 10 minutes et au moindre souci : le
  fonctionnement ne change pas.
- Nouveau journal des onduleurs (un fichier par jour, onduleurs.AAAAMMJJ.txt,
  même durée de conservation que le journal) : chaque consigne écrite dans
  Home Assistant avec la valeur relue juste après, chaque changement de mode,
  et chaque changement d'état d'un onduleur (charge, décharge, veille,
  défaut…) avec sa raison et ses mesures ; un relevé de tous les onduleurs
  toutes les 10 minutes.
- Page Diagnostic › Journaux : visionneuse du journal des onduleurs (par jour,
  par onduleur, par sorte de ligne, recherche ; échecs en rouge) et
  téléchargement des journaux de chaque jour et d'un dossier de diagnostic
  (journaux + configuration, jetons masqués) à envoyer au support. Utile en
  particulier avec l'application Home Assistant, dont les fichiers ne sont
  pas accessibles directement.
- Erreur 01 (consigne non appliquée) : beaucoup moins de fausses détections
  sur les onduleurs lents à démarrer. Délai avant l'erreur 01 de 60 s (30 s
  au minimum) et écart toléré de 100 W (50 W au minimum) : ces deux valeurs
  sont remises à leur nouvelle valeur par défaut lors de la mise à jour.
  Intervalle entre deux tests de déblocage de 5 min à 1 h (10 min par
  défaut), puissance du test de 40 à 100 W (50 W par défaut).
- Fiche de l'onduleur : nouvelle case « Inhibition de l'erreur 01/02 »
  (décochée par défaut) pour un onduleur lent à démarrer ou qui se met en
  veille de lui-même ; une erreur 01 en cours est levée dès l'enregistrement.
- Charge optimisée : « Durée sans charge avant l'arrêt » de 1 à 10 min,
  5 min par défaut (de 30 s à 5 min et 2 min avant) ; puissance des deux
  onduleurs régulants de 30 à 100 W, 100 W par défaut (de 0 à 50 W avant) :
  un onduleur lent à démarrer se réveille plus vite.
- Onduleurs lents à démarrer : le temps de démarrage de chaque onduleur
  (consigne envoyée à un onduleur au repos -> consigne vraiment prise) est
  mesuré en charge et en décharge et appris (100 derniers démarrages). Pendant
  un démarrage, il reçoit une consigne de réveil fixe et la régulation
  l'attend ; il reçoit sa part une fois réveillé. Carte de l'onduleur : « En
  démarrage depuis X s ».
- Réveil des onduleurs : nouveau réglage « Consigne de réveil d'un onduleur »
  (Paramètres › Répartition entre les onduleurs, 50 à 200 W, 50 W par
  défaut). Un onduleur qui se met à charger ou à décharger reçoit d'abord
  cette petite consigne, et les onduleurs déjà actifs prennent tout le reste,
  dans la limite de leur puissance maximale (exemple : 1 000 W à décharger,
  950 W pour l'onduleur qui travaille, 50 W pour celui qui se réveille). Ce
  qu'ils ne peuvent pas prendre va à l'onduleur qui se réveille : il démarre
  peut-être directement à cette puissance. Une fois réveillé (la moitié de sa
  consigne de réveil prise), il reçoit sa part par le « Transfert progressif
  entre onduleurs ». Avant, une part entière (« Puissance par onduleur
  actif ») lui était réservée et n'allait à personne : un creux de 600 W le
  15/10 à 22:22.
- Décharge en mode Autonome : un onduleur reste toujours actif, au moins à la
  consigne de réveil. Quand la consommation chute d'un coup, la décharge
  baisse aussitôt mais n'est plus coupée : elle repart sans attendre qu'un
  onduleur redémarre (le 15/10 à 22:22, tout s'était arrêté, 25 s pour
  s'arrêter puis 40 s pour repartir, 500 à 600 W achetés). La décharge
  s'arrête toujours par la charge (Start charge), la veille ou les batteries
  au minimum.
- Consigne en cours, correctif : la mesure d'un onduleur dans Home Assistant
  peut être en retard de plusieurs secondes. La part pas encore appliquée
  n'est plus déduite deux fois : elle ne dépasse jamais ce que le compteur
  n'en a pas encore montré, d'un cycle au suivant (le 15/10 à 22:22:16,
  974 W comptés deux fois avaient mis la consigne à 0).
- Erreur 01 aussi en charge, et pour un démarrage trop long. Délai de chaque
  onduleur : le plus long entre le réglage « Délai avant l'erreur 01 », le
  temps de réaction de sa fiche et la moyenne de ses démarrages + 5 %.
- La consigne en cours ne fait plus partir la régulation à contresens (plus
  de décharge pendant que le compteur exporte, ni l'inverse).
- Le protocole de démarrage s'applique à tous les ordres : décharge et
  charge classiques, mode Assisté, charge planifiée, charge optimisée,
  onduleurs en cluster et pendant un contournement solaire ; la charge
  classique ne part plus non plus à contresens à cause d'une consigne en
  cours.
- Nouvelle page « Diagnostic », avec une barre de rubriques :
  « Vue d'ensemble » montre chaque onduleur piloté et ses batteries empilées
  (niveau, puissance, température, tension, intensité, écart de tension entre
  cellules) ; « Temps de démarrage » montre le démarrage habituel de chaque
  onduleur en charge et en décharge et le délai avant l'erreur 01 appliqué.
  Les 100 derniers démarrages de chaque onduleur sont gardés ; un bouton
  « Réinitialiser toutes les mesures » repart de zéro (utile quand les
  onduleurs deviennent plus rapides). Les mesures d'un onduleur supprimé sont
  effacées avec lui.
- Batteries des onduleurs : trouvées automatiquement pour Zendure (AB1000S,
  AB2000X, AB3000… rattachées à leur Hyper ou SolarFlow), cherchées parmi les
  appareils rattachés à l'onduleur pour les autres marques, ou déclarées à la
  main (toutes les entités facultatives). Alerte quand l'écart de tension
  entre cellules dépasse 0,20 V (réglable, Paramètres › Surveillance), avec
  une notification par jour. Information seulement : la régulation n'en
  dépend pas.
- Barre du haut sur deux lignes : la marque et les états, puis tous les
  onglets ; ceux qui ne tiennent pas passent dans « Plus ».
- Onduleur tout juste débloqué : la page Onduleurs affiche « Prioritaire
  après déblocage (erreur 01) » avec l'heure de fin de la priorité (elle
  était invisible).
- Batteries presque pleines : nouveau réglage « Marge de redémarrage de la
  charge » (Paramètres › Batteries et puissances maximales, 2 points par
  défaut, de 0 à 10). Une batterie à moins de cette marge de son niveau
  maximal ne démarre plus de charge (sur surplus, optimisée ou planifiée) :
  près du maximum, elle ne prend que quelques dizaines de watts après un
  long démarrage. Une charge déjà commencée continue jusqu'au maximum ; un
  cluster suit la moyenne de ses batteries ; un niveau imposé par une
  automatisation (calibration) est visé sans marge. 0 = marge générale
  désactivée (la charge redémarre dès le premier point sous le maximum).
- Fiche de l'onduleur piloté : nouvelle rubrique « Redémarrage de la charge
  (batterie presque pleine) » avec sa propre marge, de 0 à 10 points (0 par
  défaut = marge générale). Renseignée, elle remplace la marge générale pour
  cet onduleur : utile quand les marques diffèrent (environ 2 points chez
  Zendure, jusqu'à 5 pour d'autres).
- Tableau de bord, bilan du jour : le « Pic de consommation » indique
  l'heure et la minute où il a été atteint (« atteint à 18h42 ») au lieu
  du nombre d'heures mesurées.
- Automatisations : chaque condition et chaque sous-groupe a une poignée.
  Glissez-la pour déplacer la condition (dans un autre groupe, en tête d'un
  sous-groupe, à la fin d'un groupe), ou cliquez dessus : monter,
  descendre, mettre dans un nouveau sous-groupe, regrouper avec la
  condition suivante, sortir du groupe, déplacer dans un autre groupe,
  dissoudre un sous-groupe. Un sous-groupe vidé par un déplacement
  disparaît ; la profondeur maximale (5 niveaux de sous-groupes) est
  respectée. Rien n'est appliqué avant « Enregistrer ».

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
