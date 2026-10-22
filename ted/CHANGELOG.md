# Journal des versions

Nouveautés détaillées de chaque version : <https://ted-energy.fr/evolutions>

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
- Connecteur « Indevolt / IsyPower / Matter France » (Paramètres › Connecteurs
  d'appareils) : même API locale chez les trois marques, nom et textes mis à
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
