# Audit objectif : son, ambiance, personnages, rythme

**Léo · 26 novembre 2026 · 20 minutes · angle : mesures, pas impressions**

**Méthode.** J'ai lu le code (`Snd`, `Voice.rec`, `BARKS`, `DEJAVU`, `say()`, `toast()`, `updateAgents`, `LIGHTS`, `render`) et joué deux soirées instrumentées en 1280×720, son et voix activés dans la sauvegarde, avec `#debug` et une accélération ×14 (`S.skipTo`). Profil « passif » : un nouveau joueur qui reste à l'entrée. Profil « baladeur » : il traverse le hall, le banc, la porte du local, le quai. Au barrage, il répond « Rien vu ». Une sonde relevait toutes les 40 ms les nouvelles bulles (`S.said`, `S.barked`), les toasts, les fiches, les `new Audio()` (voix), les créations d'oscillateurs, ainsi que la position des PNJ à chaque seconde de jeu. **Limite assumée** : le navigateur tourne sans écran ni haut-parleur. Je n'ai donc *rien entendu*. Tout ce qui concerne le son vient du code et des événements déclenchés. Aucune erreur JavaScript. Scripts, journaux JSON et captures : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur8/` (`run2.js`, `an.js`, `voix.js`, `passif.json`, `baladeur.json`, `*_h2154.png`, `*_h2156.png`, `*_h2158.png`, `*_h215930.png`).

## Tableau de bord

| Axe | Note | Ça manque ? |
|---|---|---|
| Son | **4/10** | **Oui, nettement.** Tout est synthétisé. Les 3 vrais bruitages du dépôt (`sons/`) ne sont jamais chargés, et 83 % des répliques entendues sont muettes. |
| Ambiance | **6,5/10** | **Un peu.** Le décor est beau et la pluie, les néons et la poussière vivent. Mais le hall est vide (aucun figurant) et la lumière ne change presque pas en 6 minutes. |
| Personnages | **6/10** | **Oui, en présence à l'écran.** Les fiches sont riches (≈ 105 fiches), mais en jeu les PNJ restent plantés : de 61 à 92 % de leur temps visible immobiles, sans geste. |
| Rythme | **6/10** | **Oui, au milieu.** La première minute est dense (8-9 événements par 30 s), puis on tombe sous 2 événements par 30 s de 21:56:00 à 21:58:00, avec un trou de 48 s. |

## 1. SON (4/10)

### Inventaire (code, `Snd`, l. 375-424)

| Son | Fabrication | Déclencheur |
|---|---|---|
| Nappe grave (ré, la, ré, avec LFO) | 3 oscillateurs sinus, réverbération | Continue dès « Continuer / Commencer » (`Snd.music()`) |
| Piano feutré | Oscillateurs triangle, une note toutes les 2,6 à 7,8 s | Continu |
| « Pluie » | Bruit blanc filtré 400-1400 Hz, gain **0,006** | Continu |
| Tension montante | 2 sinus qui montent | À partir de 21:58:00 (`(S.t-240)/120`) |
| Battement de cœur | 2 sinus graves | Gilet jaune à moins de 0,08, cachette, traque |
| Carillon d'annonce | 3 notes | Chaque toast 📢 (21:54:20, 21:58:00, 21:59:30) |
| Sonnerie de téléphone | Carrés 880/1100 Hz | 21:55:30 (guichet), coupure du coffret |
| Rame | Dent de scie 70→110 Hz et bruit 4 s | 21:58:45 |
| Grésillement | Bruit 0,08 s | 21:56:30 (micro-coupure), étincelles, fusibles |
| Bourdon de drone | Sinus 240 Hz, gain 0,006 | Essai de Bastien (21:55:24-36), tournage |
| Pas | Bruit 0,05 s toutes les 0,3 s | Uniquement quand Camille marche |
| Tic d'horloge | Carré 1600 Hz | Chaque seconde après 21:59:50 |
| Bruitages d'objets (`fx`) | Bruits courts | Porte, bureau, casier, poubelle, distributeur, armoire… à l'examen |
| Note d'indice, clic, blip de frappe, alarme, crash, rembobinage | Oscillateurs | Événements d'interface |

**Fait marquant n°1** : le dossier `sons/` contient `armoire.mp3` (0,5 s), `rame.mp3` (1,0 s) et `telephone.mp3` (2,0 s), mais **aucune ligne de `index.html` ne les référence** (`grep 'sons/'` ne renvoie rien). La rame et le téléphone sont joués par des oscillateurs alors que les fichiers enregistrés sont là. Le commentaire l. 388, « musique et ambiance enregistrées (boucles) », est faux : rien n'est enregistré.

**Fait marquant n°2** : la pluie est à 0,006 de gain, contre 0,03 pour la fondamentale de la nappe et 0,05 pour le piano, soit environ **14 dB sous la nappe**. Elle est sans doute à peine perceptible (estimation sur le code, non entendue). Pourtant, c'est le motif central du texte (« Quelle pluie… Comme le premier jour. »).

### Doublage : mesure sur une soirée type (profil passif)

- Bulles affichées : **22** (15 répliques scriptées et 7 « barks »), plus 2 répliques de fiche au barrage, soit **24 répliques entre guillemets**.
- Voix réellement lancées (`new Audio`) : **4**, à savoir `g_appel` (21:54:56), `o_file` (21:57:20), `g_barrage` et `g_bien` (21:58:23). Cela fait **17 % des répliques et 9,4 s de voix sur 360 s (2,6 % du temps)**.
- Pour le baladeur, le résultat est identique (4 voix sur environ 26 répliques).
- Couverture dans le code : 71 entrées `Voice.rec` pour 71 fichiers (aucun orphelin, aucun manquant). Sur 57 bulles `say()` distinctes, **11 sont doublées**. Sur 19 barks et lignes DEJAVU, **0 est doublée**. Sur 319 citations « » distinctes dans tout le code, **61 sont doublées (19 %)**.
- Restent muettes, entre autres, les premières répliques de la soirée : « Camille ! Par ici ! », « Camille ! Tu fais la ronde pour moi ? » et « Quelle pluie… Comme le premier jour. ». Le joueur passe donc **56 secondes** sans entendre une seule voix humaine.
- Le doublage est vraiment entendu surtout quand on *ouvre des fiches* (les 61 citations doublées sont presque toutes dans des fiches). Le hall, lui, reste muet.

### Silence

- **Silence absolu : 0 s.** La nappe et le piano tournent en continu dès l'écran-titre validé.
- **Silence événementiel** (aucun son ponctuel lié à ce qui se passe, nappe mise à part) dans une soirée passive : 12 sons ponctuels seulement. Le plus long trou va de **21:56:30 (grésillement) à 21:58:00 (carillon)**, soit **90 s** où seuls la nappe et le piano jouent.

### Sons attendus mais absents

| Attendu | État |
|---|---|
| Pluie audible, plus forte près de la porte vitrée | Bruit à 0,006, sans spatialisation |
| Pas des PNJ (talons de Mme Delattre, chaussures de chantier) | Absents. Seule Camille a des pas |
| Porte vitrée de l'entrée (gilet jaune à 21:54:48, Vasseur) | Absente. Le `fx.door` ne sonne qu'à l'examen de la porte du local |
| Rame qui entre, freine, portes qui s'ouvrent, départ | Une seule nappe de 4 s à 21:58:45. Rien à quai, rien au départ |
| Sonnerie de quai ou buzzer de départ (fermeture des portes) | Absent |
| Téléphone du gilet jaune (vibreur) à 21:54:56 | Absent |
| Gobelet du distributeur au café d'Odile (21:57:20) | Absent (son seulement si c'est le joueur qui achète) |
| Moteur de la berline de Vasseur (21:59:30) | Absent. Seul un toast l'annonce |
| Bourdonnement des néons, surtout celui qui flanche | Absent (on le voit flancher, on ne l'entend pas) |
| Voix des annonces 📢 | Texte et carillon seulement, pas de voix |

### 3 améliorations (impact / effort)

1. **Brancher `sons/rame.mp3`, `sons/telephone.mp3` et `sons/armoire.mp3`** à la place des oscillateurs. Impact moyen, effort minime, **0 crédit** (déjà payés).
2. **Une boucle de pluie et d'ambiance de hall enregistrée** (pluie sur verrière, bourdonnement de néon), montée à environ 0,03, plus forte près de l'entrée. Ajouter des bruitages pour la porte vitrée, le freinage et les portes de la rame, le buzzer de départ et le moteur de berline. Impact fort, effort petit. **Bruitages ElevenLabs : 6 × 17-20 ≈ 110-120 crédits.**
3. **Doubler les 15 bulles scriptées d'une soirée type et la voix des annonces** (« Camille ! Par ici ! », « Tu fais la ronde pour moi ? », « Quelle pluie… », « Allô ? », « Oh ! Les lumières… », « La voilà ! Ma petite rame… », les 3 annonces…). Environ 15 lignes × 35 caractères et 3 annonces × 60 caractères, soit **≈ 700 crédits**. Doubler aussi les 19 barks coûterait **≈ 650 crédits** de plus. Impact fort, effort petit (une ligne `rec` chacune).

## 2. AMBIANCE (6,5/10)

### Ce qui bouge à l'écran (code `render` et captures)

| Élément | Quantité | Remarque |
|---|---|---|
| Gouttes de pluie | 140 traits | **Uniquement dans 2 rectangles « dehors »** (`OUTSIDE`, à gauche). Invisibles dès qu'on est côté quai |
| Ronds dans les flaques | 3 | Devant l'entrée |
| Halos de néons | 6 | Pulsation de ±8 %. L'un flanche, et plus fort après 21:59:00 |
| Poussière dans la lumière | 60 particules | Partout |
| Étincelles | 4 par image | Seulement pendant le sabotage, dans le local |
| Dérive de caméra « à l'épaule » | Continue | ±2 px |
| Drone, rame, berline | Événementiels | 21:55:24, 21:58:45, 21:59:30 |
| Reflets des personnages au sol | 5 | Bonne idée, discrète |

### Lumière au fil de la soirée

Le fond est **une seule image fixe** (`IMG.bg`) pour les 6 minutes. Voici les variations mesurées :
- `S.dark` : 1,4 s à 21:56:30 (micro-coupure), plus les coupures déclenchées par le joueur ;
- le néon défaillant flanche plus souvent après 21:59:00 ;
- les phares de la rame entre 21:58:45 et 21:59:00 ;
- le zoom passe à 1,1 après 21:59:40.

Les captures `passif_h2154.png`, `baladeur_h2156.png`, `baladeur_h2158.png` et `baladeur_h215930.png` montrent la **même colorimétrie** du début à la fin. Il n'y a aucune bascule de lumière qui ferait monter la tension (le hall ne s'assombrit pas, aucune lumière de quai ne s'allume pour le départ, aucun gyrophare). La seule exception est le hall « en veilleuse » quand le plan B a lieu.

### Ce qui reste statique

Le fond, les écrans de surveillance (aucune animation), le tableau des départs (il ne s'éteint qu'au plan B, il n'affiche pas d'heure qui défile), le distributeur et **tout le monde quand personne ne marche** (voir § 3).

### Figurants

**Aucun.** Le hall de la « dernière rame après 27 ans » ne contient que 4 PNJ et Camille. Pas un voyageur, pas un agent, pas un passant sous la pluie. C'est cohérent avec un huis clos, mais c'est incohérent avec l'annonce « Merci pour ces vingt-sept ans » : une dernière rame historique attirerait au moins deux ou trois curieux.

### 3 améliorations (impact / effort)

1. **Une courbe de lumière sur 6 minutes, sans nouvelle image** : un filtre global qui refroidit légèrement à 21:58:00, la lumière de quai qui passe à l'ambre quand la rame entre, un voile rouge pulsé à 21:59:50. Impact fort, effort petit, **0 crédit**.
2. **Pluie visible partout** : ruissellement sur la verrière côté quai, gouttes dans la fosse (« la pluie qui tombe dans la fosse » est écrit dans la fiche du quai mais n'est pas dessiné), traces mouillées laissées par les PNJ qui entrent. Impact moyen, effort petit, **0 crédit**.
3. **2 ou 3 silhouettes de figurants** (un couple qui photographie la rame, un agent qui ferme les grilles à 21:59:30), avec un trajet simple et un bark chacun. Impact moyen, effort moyen. **Images ElevenLabs : 2 à 3 sprites × 98 ≈ 200-300 crédits**, plus environ 100 crédits si on double un bark chacun.

## 3. PERSONNAGES (6/10)

Mesures : une position par seconde de jeu sur la soirée passive. Les deux profils donnent des résultats à ±2 s près. « Immobile sans pose » veut dire que le PNJ ne bouge pas et n'a aucune pose spéciale (téléphone, travail, café, banc, drone, doigt pointé). Je n'ai trouvé dans `drawAgent` aucune animation d'attente en dehors de la marche (à confirmer à l'œil).

| Personnage | Fiches (`sheet("…")`) | Options de dialogue (≈, `{ l:`) | Bulles `say()` + barks | Voix enregistrées | Comportements autonomes scriptés | Visible | En mouvement | **Immobile sans geste** |
|---|---|---|---|---|---|---|---|---|
| Odile | 41 | ≈ 35 (dont 6 questions d'aveu) | 19 + 4 (+1 DEJAVU) | 25 | 6 : salut, ronde, contrôle des voyants (21:55:48), téléphone, café (21:57:20), retour. Plus les branches plan B, chasse, frein | 360 s | 26 s | **265 s (74 %)** |
| Gilet jaune (Gérald) | 18 | ≈ 11 | 24 + 3 (+1) | 20 | 8 : entrée, appel, attente, regard par la vitre (21:55:46), sabotage, barrage, départ. Plus les branches traque, porte bloquée, fuite, filature | 313 s | 69 s | **190 s (61 %)** |
| Bastien | 17 | ≈ 11 | 6 + 3 (+2) | 13 | 5 : banc, faire les cent pas, essai de drone, quai, embarquement | 354 s | 30 s | **232 s (66 %)** |
| Mme Delattre | 19 | ≈ 15 | 5 + 3 (+2) | 12 | 5 : arrivée, gant perdu, clé (21:56:12), micro-coupure, embarquement | 354 s | 30 s | **324 s (92 %)** |
| Vasseur | 10 | 6 | **0** | 2 | **0** : une image de voiture fixe à partir de 21:59:30 | ≈ 30 s (voiture) | 0 | 100 % |

### Arcs émotionnels (sans intervention du joueur)

- **Odile** : joyeuse (« Camille ! Par ici ! »), puis fonctionnelle (ronde, voyants), puis furtive (« À 57, comme prévu »), puis elle s'absente et revient à son poste. **Le moment de la rame qui part n'est pas joué** : aucune bulle à 21:59:30 ni à 22:00, alors que c'est le dernier départ de sa carrière. Son arc n'existe vraiment que si le joueur ouvre ses fiches (aveu, choix).
- **Gilet jaune** : l'arc le plus complet. Il est menaçant, puis concentré, puis il barre le passage, puis il part satisfait. C'est lui qui a le plus de comportements autonomes.
- **Bastien** : il est nerveux (« Allez… juste un essai. »), puis il est pris (« Rien ! Rien du tout. »), puis il attend et embarque. Arc plat après 21:55:36.
- **Mme Delattre** : nostalgique (« Comme le premier jour »), elle tente la clé, puis s'émerveille (« La voilà ! Ma petite rame… »). L'émotion est juste, mais elle reste **immobile 92 % du temps, sans aucune pose** (zéro geste mesuré en soirée passive). C'est le personnage qui porte la mémoire de la station, et c'est le plus figé.
- **Vasseur** : aucun arc. Il apparaît sous forme de toast et d'image.

### Figurants

**Zéro.** Voir § 2.

### 3 améliorations (impact / effort)

1. **Des poses d'attente qui tournent** (toutes les 15 à 20 s) avec les images déjà en stock : Delattre qui pointe vers la voie (`delattre_point`), Bastien assis ou drone (`bastien_sit`), Odile au téléphone ou au café, le gilet jaune au téléphone. On réutilise les sprites existants. Impact fort, effort petit, **0 crédit**.
2. **Un « moment de départ » pour chacun à 21:59:30-22:00** : Odile pose la main sur le pupitre (« Vingt-sept ans… »), Delattre ôte son chapeau, Bastien lève son téléphone. Ce sont 3 bulles. Impact fort (émotion de fin), effort petit. **Voix : 3 × 30 caractères ≈ 90 crédits.**
3. **Faire exister Vasseur avant 21:59:30** : il appelle le gilet jaune à 21:57:30 (« Alors ? »), on entend la voix filtrée au téléphone et on voit ses phares passer dehors à 21:56:00. Impact moyen, effort petit. **Voix ≈ 60 crédits, bruitage moteur ≈ 20 crédits.**

## 4. RYTHME (6/10)

### Chronologie mesurée, nouveau joueur passif

Les bulles, toasts et fiches sont mesurés. Les « actions possibles » viennent des points d'intérêt visibles et des fiches ouvrables.

| Tranche | Événements (bulles / toasts / fiches) | Contenu | Densité 0-3 |
|---|---|---|---|
| 21:54:00-21:54:30 | **8** (3 / 2 / 0, + 3 barks) | Salut, ronde, annonce 1, barks | 3 |
| 21:54:30-21:55:00 | **9** (5 / 2 / 0, + 1 voix) | Pluie, gant, appel « monsieur Vasseur » | 3 |
| 21:55:00-21:55:30 | **4** | Pièce tombée de Bastien, « Plus qu'à attendre » | 2 |
| 21:55:30-21:56:00 | **7** | Drone, téléphone du guichet, « À 57 », voyants | 3 |
| 21:56:00-21:56:30 | **2** | Clé de Delattre (21:56:16) | 1 |
| 21:56:30-21:57:00 | **2** | Micro-coupure, « Ce coffret… » | 1 |
| 21:57:00-21:57:30 | **1** | Odile part au café (voix) | 1 |
| 21:57:30-21:58:00 | **0 visible** | Le gilet jaune entre dans le local (seulement dans le journal) | **0** |
| 21:58:00-21:58:30 | **6** | Annonce 2, barrage (fiche, voix, choix), toast | 3 |
| 21:58:30-21:59:00 | **1** (son de rame) | La rame arrive | 1 |
| 21:59:00-21:59:30 | **1** | « La voilà ! Ma petite rame… » | 1 |
| 21:59:30-22:00:00 | **2** | Berline, annonce 3, tic-tac | 1 |

- **Total** : environ 43 événements, soit 7,2 par minute. Mais **63 % sont dans les 2 premières minutes**.
- **Plus long intervalle sans bulle, toast ni fiche** : **48 s** (passif, 21:56:32 → 21:57:20), puis **40 s** (21:57:20 → 21:58:00). Pour le baladeur, les barks comblent en partie, mais il reste un trou de **34 s** (21:57:26 → 21:58:00).
- Les 3 minutes du milieu (21:56:00 → 21:58:00), qui sont justement celles du **sabotage**, ne font que **5 événements**. L'action principale (le gilet jaune dans le local) n'émet ni son, ni toast, ni bulle pour qui n'est pas devant la porte.

### Comparaison avec le genre (honnête)

Je n'ai pas mesuré ces jeux avec le même outil. Ce sont des ordres de grandeur tirés de mon expérience de joueur.
- **Twelve Minutes** : une boucle de 10 à 12 minutes, 3 PNJ, un appartement. Presque chaque seconde offre un objet manipulable, et le mari, la femme et le policier **réagissent en continu** à ce que vous faites. Sa densité *réactive* est plus forte. Sa densité *scriptée*, elle, n'est pas plus forte : il a lui aussi de longs passages d'attente (la femme qui cuisine). **La Dernière Rame fait jeu égal sur la première minute, et moins bien sur le milieu.**
- **Return of the Obra Dinn** : pas de chronomètre. La densité vient de la déduction, pas des événements, donc la comparaison n'est pas pertinente pour le rythme. Le jeu montre en revanche qu'une **scène figée mais très sonore** (chaque souvenir commence par 10 à 20 s de son seul) crée de l'ambiance sans animation. C'est précisément ce qui manque ici.
- **Outer Wilds** (une boucle de 22 minutes) : le rythme est porté par des **jalons sonores et visuels globaux** visibles de partout (la musique de la supernova, l'effondrement du Sablier). L'équivalent ici serait un jalon perceptible depuis tout le hall à 21:57:00 et à 21:58:00. Il n'y en a pas pour le sabotage.

### 3 améliorations (impact / effort)

1. **Rendre le sabotage perceptible depuis le hall** entre 21:57:30 et 21:58:00 : coups métalliques sourds venant du local (`sons/armoire.mp3` existe déjà), un néon du hall qui faiblit à chaque coup, un toast unique « Un bruit métallique, derrière la vitre du local. ». Cela bouche le trou de 40 à 48 s. Impact fort, effort petit, **0 crédit**.
2. **Un événement par tranche vide** : à 21:56:45, Bastien laisse tomber son téléphone qui sonne. À 21:57:40, Delattre vient demander l'heure à Camille (choix rapide). À 21:59:10, les portes de la rame s'ouvrent avec un buzzer. Impact fort, effort moyen. **≈ 2 bruitages (40 crédits) et 2 répliques (≈ 80 crédits).**
3. **Un barks plus fréquent au milieu** : `barkT` passe de 9-16 s à 6-10 s entre 21:56:00 et 21:58:00, et 3 nouveaux barks « de situation » sont ajoutés (Delattre : « Il est long, ce café… », Bastien : « Il fait quoi, lui, dans le local ? »). Impact moyen, effort minime, 0 crédit en texte seul, **≈ 100 crédits** si on les double.

## Conclusion : les 3 manques les plus importants

1. **Le SON, en priorité.** Il y a 0 bruitage enregistré dans le jeu alors que 3 attendent dans `sons/`. La pluie est quasi inaudible. 17 % des répliques entendues sont doublées et le hall n'a aucune voix pendant les 56 premières secondes. C'est le meilleur rapport impact/coût : brancher les 3 fichiers ne coûte rien, et environ 820 crédits (6 bruitages + 15 bulles + 3 annonces) changeraient la perception de tout le jeu.
2. **Le RYTHME du milieu.** De 21:56:00 à 21:58:00, il y a 5 événements en 2 minutes, avec un trou de 48 s, *pendant* le sabotage, qui reste invisible et inaudible depuis le hall. C'est corrigeable sans crédit (coups métalliques, néon qui faiblit, un toast).
3. **La PRÉSENCE des personnages.** Ils sont riches en fiches (≈ 105 fiches, 71 voix) mais figés à l'écran (Delattre immobile 92 % du temps), sans figurant et sans moment de départ à 22:00. Faire tourner les poses existantes et ajouter 3 bulles de départ (≈ 90 crédits) suffirait à les faire vivre.

L'ambiance visuelle est le point le plus solide (le décor, les reflets, les néons, la poussière). Elle a seulement besoin d'une courbe de lumière sur les 6 minutes.

---

# Retours de playtest : Version 11 · tour fun

**Léo · 25 novembre 2026 · environ 15 minutes · angle : le FUN**

Ce que j'ai joué : 1280×720 et iPhone 13, son et voix activés, vrais clics sur les personnages et sur les boutons des fiches (0,7 s d'attente). `#debug` m'a servi uniquement à sauter dans le temps et à poser des objets (café, enveloppe, indices). Scénarios joués : la clé de Mme Delattre (21:56:15), le café offert puis le plan B au coffret (21:58:02), le barrage avec chacune des trois réponses (21:58:20, trois parties) et l'enveloppe montrée à la vitre (21:58:46). Aucune erreur JavaScript. Scripts, journaux et captures : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur7/` (`s.js`, `*.out`, `*.png`).

## Verdict en 3 lignes

Le barrage est maintenant **le meilleur moment de la soirée**. L'illustration (ses deux mains gantées tendues vers vous, la goupille rouge dans sa poche), la voix et trois réponses **sans « Partir »** donnent un vrai frisson, et chaque réponse a un effet visible dans les 5 secondes qui suivent.
« Rien vu » suivi du vol de la goupille, c'est le coup de poker que j'attendais. On ment, il baisse sa garde, on le fait les poches. C'est drôle, c'est malin, et ça sert l'enquête.
En revanche, les bandeaux restent **décoratifs** sur la clé et le coffret, et le jeu fait encore très peu rire. Le fun repose sur 2 ou 3 gestes. Tout le reste, c'est lire puis choisir.

## Notes sur 10 (tour 5 → tour fun)

| Critère | T5 | Fun | Pourquoi |
|---|---|---|---|
| Quantité | 8,5 | **8,5** | Rien de nouveau entre 21:58:30 et 21:59:40, sauf si on a répondu « Rien vu » (vol de la goupille) ou demandé « Qu'est-ce que vous faisiez » (il vous suit). |
| Fun des actions | 8 | **8,5** | « Rien vu » puis « Lui prendre la goupille qui dépasse de sa poche » : mentir pour mieux voler est le premier geste vraiment *malicieux* du jeu. |
| Tension | 8 | **8,5** | Le bandeau du barrage fait peur. « Je vous tiens à l'œil. » suivi d'une filature *inversée* (c'est lui qui vous suit) est une excellente idée. |
| Lisibilité | 7,5 | **7,5** | Les conséquences se voient tout de suite, mais la goupille volée n'est annoncée que par du texte (« Avec elle, le levier du frein tiendra. »). On ne sait pas où l'utiliser. |
| Conséquences | 8,5 | **9** | Les 3 réponses mènent à 3 suites distinctes : il se calme, il vous colle ou il fuit vers la sortie (vu à 21:58:30, `bump3_e.png`). C'est exactement ce que je demandais. |
| Rejouabilité | 7,5 | **8** | J'ai *envie* de refaire le barrage pour voir les deux autres branches. C'est le premier choix du jeu qui pousse à relancer. |
| Finition / mobile | 7,5 | **7,5** | Les bandeaux s'affichent bien sur iPhone (`m_bump2_b.png`), mais le texte de la fiche y devient minuscule. Le bandeau du coffret est coupé par l'annonce (bug 1). |

## Ce que valent les bandeaux et les voix

| Bandeau | Verdict |
|---|---|
| **Barrage** (`scene_barrage.jpg`) | **Excellent.** C'est l'image la plus forte du jeu : on voit les mains, la capuche de Camille au premier plan et la goupille rouge. Elle *montre* la menace au lieu de la décrire. Elle plante aussi l'indice visuellement avant « Regarder ses mains ». C'est bien vu. |
| **Coffret** (`scene_coffret.jpg`) | Bon, et cohérent : on voit le gilet jaune dans le local en arrière-plan pendant qu'Odile fouille les fusibles. C'est le plan B raconté en une image. En revanche, le bandeau est **rogné en haut** par l'annonce (bug 1). |
| **Clé** (`scene_cle.jpg`) | Joli et tendre, mais il arrive **après** le geste. Le bouton « L'aider à tourner la clé » ne fait rien jouer : on clique, on lit. C'est celui qui gagnerait le plus à devenir un geste (proposition 2). |
| **Vitre** (`scene_vitre.jpg`) | **Non vu.** En montrant l'enveloppe par l'option de dialogue « Lui montrer l'enveloppe de « V. » », on obtient une fiche classique sans bandeau. Je n'ai pas réuni les conditions de `confrontation()`, donc je ne juge pas cette scène. |

**Voix** : les fichiers attendus se chargent au bon moment : `g_barrage`, `g_bien`, `g_boulot` et `g_quoi` pour le barrage, `d_henri` pour la clé, `o_pense` pour le café, `o_tableau` et `o_file` pour le plan B, `o_rien` pour l'enveloppe. Ils sont tous présents dans `voix/` et différents les uns des autres. Mon navigateur sans écran ne me permet pas de juger le jeu des acteurs à l'oreille. Une remarque quand même : « Quoi ? » (`g_quoi`) est une réplique très courte pour un crédit. La voix sert surtout sur « Vous n'avez rien vu, hein ? », et là elle est indispensable.

## Bugs précis

1. **Coffret, 21:58:02 (ordinateur)** : l'annonce « ANNONCE ▸ « La dernière rame entrera en gare dans quelques instants. » » s'affiche par-dessus la fiche, et le bandeau du coffret est **coupé de moitié** (on ne voit que les mains et la lampe, `coffret1.png`). Il faudrait masquer l'annonce quand une fiche illustrée est ouverte, ou la décaler.
2. **Barrage : la scène ne correspond pas à l'image de fond.** Le texte dit « tout près », mais le canevas derrière la fiche le montre encore dans l'encadrement de la porte du local, à 2 mètres de Camille (`bump1_b.png`). Il suffirait de figer la caméra sur les deux personnages, ou de flouter le fond.
3. **Après « Rien vu »**, la fiche du gilet jaune propose toujours « « Je sais ce que vous avez fait dans le local. » ». C'est logique, mais rien ne signale que **ça trahit le mensonge**. Une réplique dédiée (« Tiens. Tout à l'heure, t'avais rien vu. ») transformerait l'incohérence en moment de jeu.
4. **Vol de la goupille trop facile** : mon script a réussi du premier coup, en lâchant à la première alerte « regarde la rame ». Pour un « coup de poker », il manque un vrai risque : une seule fenêtre plus courte, ou une seconde tentative impossible.
5. **« Regarder ses mains »** : à 21:58:30, il marche vers la sortie (`bump3_e.png`), mais aucun toast ne le dit. Un joueur qui regarde ailleurs ne saura pas qu'il a fait fuir le saboteur, alors que c'est une conséquence énorme. Il faudrait un toast « Il est parti. Le local est libre. »
6. **Branche « Qu'est-ce que vous faisiez »** : la filature inversée est confirmée (il vient se placer à 0,09 de Camille, et « Je vous tiens à l'œil. » s'affiche). En revanche, mon script s'est bloqué avant d'avoir testé en jeu le refus de la porte et l'option « « Maman, cet homme me suit partout. » ». Je les ai **vérifiés uniquement en lisant le code** : à revalider.
7. **iPhone 13** : avec le bandeau et le portrait, le texte de la fiche descend à environ 9 px et les options sont serrées (`m_bump2_b.png`). C'est jouable au doigt, mais à la limite.

## 6 propositions de FUN (impact / effort)

| # | Quoi, quand, quel geste | Pourquoi c'est fun | Coût | Crédits ElevenLabs |
|---|---|---|---|---|
| 1 | **Le gilet jaune remarque le mensonge** (après « Rien vu », si l'on choisit ensuite « Je sais ce que vous avez fait ») : « Tiens. Tout à l'heure, t'avais rien vu. » Il passe en mode « il vous suit ». | Le mensonge devient un pari qui peut se retourner. Ça crée un petit rire jaune et ça récompense l'attention. | Petit | Optionnel : 1 réplique (~60 crédits) |
| 2 | **La clé devient un geste** (21:56:12–21:56:42) : maintenir le bouton pour forcer à deux, une jauge qui tremble, puis « crac », rien ne bouge. Elle rit. Réutiliser le mécanisme « maintenir » du vol de badge. | Le bandeau arriverait en *récompense* d'un effort raté. C'est un échec drôle et tendre, le seul moment de chaleur du hall. | Petit | Non |
| 3 | **La goupille volée s'utilise vraiment** : à l'armoire du local, l'option « Remettre la goupille neuve » avec un petit geste de visée (l'aligner dans le trou pendant que la rame entre en gare). | Ça boucle « Rien vu → vol → réparation » en une chaîne de 3 gestes : le meilleur arc de la soirée, sans dialogue. | Moyen | Non |
| 4 | **Toasts de conséquence immédiats** après le barrage : « Il quitte la station. » (mains), « Il ne vous lâche plus. » avec une silhouette visible derrière vous (question), « Il siffle en regardant le quai. » (rien vu). | Le choix se *voit* dans le hall et pas seulement dans la fiche. Ça ne coûte presque rien. | Petit | Non |
| 5 | **Easter egg Bastien** : s'il a filmé avec le drone et que vous avez répondu « Rien vu », il passe vous voir à 21:59:10 : « Rien vu ? Moi j'ai tout vu, et c'est en 4K. » Il propose de vous montrer la vidéo. | C'est le premier vrai rire du jeu, et un renvoi entre deux personnages. Ça récompense ceux qui ont aidé Bastien. | Petit | 1 réplique (~70 crédits) |
| 6 | **Odile chasse le gilet jaune en direct** (branche « il vous suit ») : au lieu d'une fiche, Odile sort de la cabine, se plante devant lui dans le hall et il recule, avec un petit tremblement d'écran et « Laissez mon enfant tranquille » doublé. | La mère protège l'enfant à l'écran. C'est un moment d'émotion et de jubilation, et c'est le payoff de la branche la plus risquée. | Moyen | 1 à 2 répliques (~150 crédits) |

Budget voix si l'on fait 1, 5 et 6 : environ 300 crédits sur les 900 disponibles. Les propositions 2, 3 et 4 n'en demandent aucun.

## Ce qu'il ne faut surtout pas toucher

- Le **barrage sans « Partir »** et ses trois suites : c'est le meilleur choix du jeu.
- L'**illustration du barrage** : elle montre la goupille avant qu'on la cherche.
- « Il essuie ses mains noires de graisse sur son gilet, lentement, sans vous quitter des yeux. » : une phrase parfaite.
- La **filature inversée** (« Je vous tiens à l'œil. ») : c'est une idée à creuser, pas à couper.

---

# Retours de playtest : Version 11 · tour 5

**Léo · 25 novembre 2026 · environ 15 minutes · angle : fun et quantité**

Ce que j'ai fait : une soirée de nouveau joueur (sauvegarde vide, 1280×720, son et voix activés, vrais clics avec 0,5 s d'attente sur les boutons). J'ai joué en temps réel de 21:54:00 à 21:54:35 (ronde), de 21:55:16 à 21:55:48 (filature) et de 21:57:20 à 21:58:23 (sabotage puis barrage). `#debug` m'a servi pour les sauts de temps. Ensuite, une vraie fin forcée (`fixed`, `odileConfess`, `envShown`, 3 boucles) suivie de « Continuer la nuit », sur ordinateur puis sur iPhone 13. Aucune erreur JavaScript. Scripts, journaux et captures : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur6/` (`s2.js`/`s2.log` pour la soirée, `s3.js`/`s3.log`/`s3m.log` pour la fin).

## Verdict en 3 lignes

La première minute est désormais pleine. Le signe de maman, l'accord pour la ronde, trois vérifications et le compte rendu (« Rien à signaler ? » avec deux réponses, dont une qui fait mentir maman) font cinq gestes en 35 s, avec un indice et un succès à la clé.
Le barrage de 21:58 comble enfin le trou de fin de soirée. Il tombe 4 s après le sabotage, met de la peur et rapporte un indice (« Une petite goupille rouge dépasse de sa poche de poitrine »).
L'écran final donne enfin envie de rejouer (« Il reste des nuits à vivre : · ??? — Deux pièces, et une mère qui ne bouge plus. »). Mais cette liste est cachée sous le générique, et « Continuer la nuit » repart comme une soirée banale.

## Notes sur 10 (tour 4 → tour 5)

| Critère | T4 | T5 | Pourquoi |
|---|---|---|---|
| Quantité | 8 | **8,5** | La ronde occupe 21:54:12–21:54:35 et le barrage occupe 21:58:19–21:58:30. Il reste un creux de 21:58:30 à 21:59:40. |
| Fun des actions | 8 | **8** | Le barrage est un vrai choix sous pression. La ronde reste une liste de cases, mais son compte rendu est savoureux (« Elle détourne les yeux une seconde de trop. »). |
| Tension | 7,5 | **8** | « Il vous barre le passage, tout près. Il sent la graisse et la pluie. » : premier vrai face-à-face menaçant d'une première soirée. |
| Lisibilité | 7 | **7,5** | La ronde ne s'impose plus et la filature se lit bien (`b05_regard.png`). L'objectif « La ronde est faite : allez le dire à votre mère, à sa vitre. » guide sans rien dévoiler. |
| Conséquences | 8,5 | **8,5** | Le compte rendu de ronde donne un indice (« La porte du local n'était pas verrouillée »). Je n'ai pas testé l'effet des trois réponses du barrage sur la suite. |
| Rejouabilité | 6,5 | **7,5** | Les pistes des fins manquantes sont excellentes, et le choix entre « Continuer la nuit » et « Rejouer depuis le début » est le bon. Il faut faire défiler l'écran pour les voir (bug 1). |
| Finition / mobile | 7,5 | **7,5** | Rien de cassé sur iPhone : le dilemme et l'écran final se touchent bien. Je relève quelques détails (bugs 2 à 5). |

## Corrections vérifiées ou non

| Correction | État | Preuve |
|---|---|---|
| La ronde se propose (21:54:12) | **Vérifiée.** À 21:54:14, le toast dit « Votre mère vous fait signe depuis sa vitre : elle a un service à vous demander. » et l'objectif reste « Allez la saluer ». La ronde n'apparaît qu'après « « D'accord pour la ronde. » ». Le succès `ronde` tombe à 21:54:34 après « « La ronde est faite. » ». | `s2.log`, `b01_rapport.png` |
| Barrage du gilet jaune après le sabotage | **Vérifiée.** Le sabotage finit à 21:58:19 et Camille est dans le hall. À 21:58:23, la fiche « Vous n'avez rien vu, hein ? » s'ouvre avec 3 réponses + « Partir ». « Regarder ses mains » donne l'indice de la goupille. | `b07_barre.png`, `b08.png` |
| Écran final : fins manquantes et « Continuer la nuit » | **Vérifiée, avec réserves** (bug 1). On y lit 5 pistes, puis « et 4 autres… » et « Souvenirs du POMA : 0/5 ». « Continuer la nuit » relance à 21:54 avec le toast « 21 h 54. La nuit continue. Le carnet garde tout. » et la boucle passe à 4. | `c03_fin_bas.png`, `c04_continue.png` |
| Filature : plus de bulle de tutoriel ni d'étiquette figée | **Vérifiée.** Le statut « ⚠️ Il ralentit et tourne la tête… · repéré 0/2 » est lisible et aucune aide « Touchez » ne s'affiche. Le succès est obtenu à 21:55:48. | `b05_regard.png` |
| Plus de nom flottant sans personnage | **Vérifiée sur mes captures** : aucun nom isolé près du distributeur de boissons à 21:55:20, 21:56:30 ou 21:58:23. Voir quand même le bug 4. | `b05_regard.png`, `b_150.png` |

## Bugs précis

1. **Écran final (ordinateur et iPhone) : la liste « Il reste des nuits à vivre » est sous le pli.** Elle vient **après** l'épilogue et le générique. À 1280×720, il faut faire défiler l'écran pour la voir, ainsi que « Continuer la nuit » (`c02_fin.png` comparé à `c03_fin_bas.png`). La mention « Fiction : le POMA 2000… » apparaît aussi **deux fois** : une fois dans le générique, une fois dans la ligne « Fins découvertes ». Je propose de remonter la liste juste sous les statistiques.
2. **« Continuer la nuit » ne marque pas la différence.** Après la vraie fin, maman repropose « « D'accord pour la ronde. » » comme si de rien n'était. Mon état était forcé (0 indice), donc l'objectif affiché ne prouve rien. En revanche, aucune ligne ne reconnaît que la boucle a été « brisée » puis rouverte.
3. **Barrage : l'option « Partir » affaiblit la scène.** Il « vous barre le passage », mais on peut partir sans répondre. Je propose de la retirer, ou de la remplacer par « Le contourner sans un mot ».
4. **21:55:20, étiquettes superposées** : « Odile » s'affiche juste sous « Bastien », alors qu'Odile est derrière sa vitre, hors du hall (`b05_regard.png`). Ce n'est pas le bug du distributeur, mais c'est la même famille.
5. **Épilogue variable pour un état identique** : avec le même état forcé, Gérald « a disparu dans la nuit… sur un chantier à Reims » sur ordinateur, mais « a été arrêté sur le quai. Il a parlé dès le premier soir. » sur iPhone. C'est peut-être voulu (aléatoire, ou lié à `gerCaught`). Pouvez-vous me dire si c'est le cas ?
6. **À vérifier** : à 21:58:23, une goupille rouge traîne par terre dans le hall (`b07_barre.png`), au moment où le texte la montre « dans sa poche de poitrine ». S'agit-il de deux goupilles différentes ? Si oui, la deuxième mériterait une phrase.

## 6 propositions, classées impact / effort

| # | Quoi | Impact | Effort | ElevenLabs |
|---|---|---|---|---|
| 1 | **Remonter le bloc « Il reste des nuits à vivre » et les deux boutons** juste sous les statistiques, avant l'épilogue et le générique. Supprimer la mention « Fiction » en double. | fort | petit | non |
| 2 | **Combler 21:58:30–21:59:40** : après le barrage, faire réagir quelqu'un selon la réponse choisie. Par exemple, Mme Delattre demande « Il vous a fait peur, ce monsieur ? » si l'on a répondu « Rien vu. », et le gilet jaune revient surveiller Camille au quai si l'on a posé la question sur le local. | fort | moyen | non |
| 3 | **« Continuer la nuit » qui se sent** : une réplique spéciale de maman la première fois (« Tu me regardes comme si tu m'avais déjà dit adieu. ») et un objectif tiré de la première piste manquante. | moyen | petit | non |
| 4 | **Barrage sans « Partir »**, avec un compte à rebours de 5 s comme dans la cabine. Ne pas répondre vaudrait « Rien vu. ». | moyen | petit | non |
| 5 | **Ronde à double fond** : à la deuxième soirée, la case « porte du local » révèle autre chose si on la coche **après** 21:57:30 (par exemple la sacoche posée devant la porte). Le même geste raconte la soirée sous un autre angle. | moyen | moyen | non |
| 6 | **Voix pour le barrage** : la réplique « Vous n'avez rien vu, hein ? » plus une réponse (« Mon boulot. »), soit 2 répliques de quelques secondes. C'est le moment le plus menaçant du jeu, et il est muet. | moyen | petit | **oui** (2 répliques) |

---

# Retours de playtest : Version 11 · tour 4

**Léo · 25 novembre 2026 · environ 15 minutes · angle : fun et quantité**

Ce que j'ai fait : une soirée de nouveau joueur (sauvegarde vide, 1280×720, vrais clics, et j'attends 0,5 s avant de cliquer un bouton de fiche), dont une filature en temps réel de 21:55:37 à 21:56:11. Ensuite, une soirée de joueur qui sait sur iPhone 13 (boucle 3, indices `chute`, `cafe`, `local` et `drone` posés, son et voix activés). `#debug` ne m'a servi qu'à avancer le temps. Aucune erreur JavaScript. Scripts, journaux et captures : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur5/` (`s1.js`/`s1.log`, `s2.js`/`s2.log`).

## Verdict en 3 lignes

La filature est la bonne surprise de ce tour : un vrai geste, avec du rythme (« Il ralentit et tourne la tête… · repéré 0/2 », puis « 👀 Regarder ailleurs »), qui rapporte un indice et un succès. Il en faudrait trois comme elle.
La première soirée à l'aveugle reste en deux temps : la première moitié est dense (de 21:54 à 21:56:30), la seconde est creuse (de 21:57:40 à 21:59:40 : les fiches sont à lire et le quai ne sert à rien).
Après la vraie fin, rien ne pousse à rejouer. Il n'y a aucun contenu « après la fin », et les souvenirs (« 1/5 ») et les succès ne sont pas mis en avant.

## Notes sur 10 (tour 3 → tour 4)

| Critère | T3 | T4 | Pourquoi |
|---|---|---|---|
| Quantité | 8 | **8** | La première moitié est riche (Bastien, drone, ticket inaugural, filature), mais je compte encore 2 min creuses en fin de première soirée. |
| Fun des actions | 7,5 | **8** | La filature (4 regards esquivés en 34 s) a un vrai rythme. Avec le cadrage et la vitre, cela fait trois vrais gestes. |
| Tension | 7,5 | **7,5** | Inchangée. Le tic sonore de la filature et le compteur « repéré 0/2 » marchent bien. |
| Lisibilité | 7 | **7** | En boucle 3, l'objectif oriente bien : « Qu'a fait le gilet jaune dans l'armoire ? Allez voir après son passage. Méfiez-vous : il n'est pas le seul à avoir l'air louche. » La bulle d'aide masque encore le statut de filature (bug 1). |
| Conséquences | 8,5 | **8,5** | Inchangées. La filature débloque « « Je sais qui va saboter la rame. » » chez maman dès la première soirée (21:56:47) : bonne réaction en chaîne. |
| Rejouabilité | 7 | **6,5** | Le compteur de souvenirs (« Souvenirs du POMA : 1/5 ») est une super idée, mais il est invisible à l'écran de fin. Rien n'appelle l'autre fin ni les succès manqués. |
| Finition / mobile | 7,5 | **7,5** | Rien de cassé sur iPhone. Je relève deux défauts d'affichage sur ordinateur (bugs 1 et 2). |

## Corrections du tour 3 : vérifiées ou non

| Correction | État | Preuve |
|---|---|---|
| Filature (≥ 15 s, au moins un regard esquivé) | **Vérifiée en jeu.** J'ai cliqué « Le suivre discrètement » à 21:55:37 (toast « Vous le suivez. S'il se retourne, regardez ailleurs. »), puis cliqué « 👀 Regarder ailleurs » à 21:55:40, 21:55:48, 21:55:57 et 21:56:04. Résultat : 4 esquives, 0 repérage, succès `filature` et indice « En filant le gilet jaune, vous l'avez vu fixer le local et compter à mi-voix… ». | `a05_regard.png`, `s1.log` |
| Étiquette / souvenir derrière le distributeur | **Vérifiée en jeu, à préciser.** Le distributeur de **boissons** n'a rien à fouiller (« Café : 2 €. Le moteur ronronne dans la pénombre. », seulement « Partir », à 21:55:05). Au distributeur de **billets**, « Fouiller le bac à monnaie » donne « SOUVENIR — LE TICKET INAUGURAL … « POMA 2000 — voyage inaugural — 4 février 1989 » … (Souvenirs du POMA : 1/5…) ». Si une étiquette est censée se cacher derrière le distributeur de boissons, je ne l'ai pas trouvée : pouvez-vous me dire ce qui était prévu ? | `a03_souv.png` |
| Ronde présentée comme une demande | **En partie.** Le texte est bon : « Votre mère vous demande une ronde, si vous voulez : ☐ bord du quai · … ». Mais il s'affiche toujours tout seul à 21:54:30, sans accord du joueur. Il disparaît à 21:57:40 (limite `S.t < 200`), alors que le quai, vérifié à 21:59:13, affiche encore « Ronde : le quai, c'est fait. » pour une ronde qui n'existe plus à l'écran. En boucle 3, il ne s'impose plus. | `s1.log`, `s2.log` |
| « 1 boucle » au singulier, rang | **Dans le code seulement** : `M.loops > 1 ? "boucles" : "boucle"` et « Détective légendaire » exige maintenant 3 déductions. Je n'ai pas eu le temps de rejouer jusqu'à l'écran de fin. | `index.html` l. 1513-1514 |
| Bureau lu seulement dans le local | **Dans le code seulement** (toast « Le bureau est dans le local : il faut y entrer pour lire quoi que ce soit. », sinon Camille y marche). En jeu, mon saut de temps à 21:57:06 a laissé maman à son poste. Le point « Bureau » était donc masqué et mon toucher a ouvert la fiche d'Odile, ce qui est un comportement normal. | `m03_bureau.png` |
| Flèches touchables sur iPhone | **Non vérifiée en jeu** : à 21:54:02 et 21:57:06, aucun personnage n'était hors champ. Le code accepte un toucher à moins de 34 px d'une flèche. | `index.html` l. 1425 |

## Bugs précis

1. **21:55:40, filature (ordinateur)** : la bulle « 👆 Touchez le sol pour vous déplacer. » est posée **sur** le statut « ⚠️ Il ralentit et tourne la tête… · repéré 0/2 ». On lit mal l'information la plus importante du mini-jeu. Par ailleurs, l'aide dit « Touchez » sur un écran à la souris. (`a05_regard.png`)
2. **21:55:40, même capture** : une étiquette « L'homme au gilet jaune » reste affichée à gauche, sur Mme Delattre, alors que le gilet jaune est à droite (marqué « Gilet jaune »). C'est sans doute le survol resté à la dernière position de la souris pendant que la caméra défile.
3. **21:59:13, bord du quai** : « Ronde : le quai, c'est fait. » s'affiche alors que l'objectif de ronde a disparu depuis 21:57:40. Le joueur ne sait pas à qui la rapporter.
4. **Écran de fin de la première soirée** : « Il recommencera. Vous le savez. Le monde se replie sur lui-même. » La boucle n'est pas nommée et la règle d'or est respectée. « Vous le savez » est juste à la limite ; je le laisserais tel quel.

## Où je m'ennuie encore (soirée 1, à l'aveugle)

- **21:54:00–21:56:30 : densité 3.** Maman, Bastien et son sac, le drone qui tombe (« Quelque chose est tombé de la poche de Bastien. »), le ticket inaugural, l'appel du gilet jaune, la filature. C'est très bien.
- **21:56:30–21:57:40 : densité 2.** On apprend des choses, mais en lisant des fiches.
- **21:57:40–21:59:40 : densité 1, temps mort.** Le gilet jaune « fixe le quai ». Maman est au café. Le quai et la rame ne donnent que du texte. Le nouveau joueur ne sait pas que c'est **là** que tout se joue. Il attend le départ.
- **Après la vraie fin** : je n'ai aucune envie de relancer « Rejouer depuis le début ». Je n'ai ni la liste de ce que j'ai raté, ni l'autre fin à découvrir, ni de défi.

## 6 propositions, classées impact / effort

| # | Quoi | Impact | Effort | ElevenLabs |
|---|---|---|---|---|
| 1 | **Écran de fin, version « rejouer »** : afficher « Fins 1/2 · Souvenirs 1/5 · Succès x/y » avec, pour chaque élément manqué, une piste d'une ligne (« Un ticket de 1989 dort quelque part au fond d'une machine… »). Le joueur qui sait repart chasser. | fort | petit | non |
| 2 | **Fin de première soirée, de 21:57:40 à 21:59:40** : un événement qui force un choix, par exemple le gilet jaune qui demande à Camille de « surveiller la porte deux minutes ». Accepter, refuser ou mentir, avec un effet visible sur le sabotage. Le creux devient une tension. | fort | moyen | non |
| 3 | **Corriger l'affichage de la filature** (bugs 1 et 2) : masquer l'aide « Touchez le sol » pendant la filature, dire « Cliquez » à la souris, effacer le survol quand la caméra bouge. | moyen | petit | non |
| 4 | **Ronde acceptée, pas imposée** : l'objectif n'apparaît qu'après une réponse à « Tu fais la ronde pour moi ? ». Si le joueur la termine, maman offre une réplique ou un objet (la pièce de 2 €, par exemple). La ronde doit récompenser, sinon c'est une liste de courses. | moyen | petit | non |
| 5 | **Mode « Dernière rame » après la vraie fin** : soirée chronométrée en partant des indices acquis, avec un objectif « vérité avant 21:58:30 » et un succès. Cela donne une raison de rejouer pour la vitesse, comme dans Obra Dinn ou Outer Wilds en speedrun. | moyen | moyen | non |
| 6 | **Deux autres souvenirs racontés à voix haute** (Mme Delattre sur Henri devant l'extincteur de 1997, maman sur le voyage inaugural de 1989) : 2 répliques de 10 s qui donnent du poids à la chasse aux souvenirs. | moyen | petit | **oui** (2 répliques) |

---

# Retours de playtest : Version 11 · tour 3

**Léo · 25 novembre 2026 · environ 15 minutes**

Ce que j'ai fait : deux soirées « expertes » complètes en 1280×720, l'une jusqu'à « La vérité », l'autre jusqu'à « Le silence ». J'ai suivi le chemin conseillé de RESOLUTION.html (sac, drone, cadrage, clé, fusible, enveloppe confiée, bureau, vidéo, vitre). J'ai ajouté un test de Bastien sans l'indice `local`, un passage à bord et deux tests sur iPhone 13 (gilet jaune, plan B). Avec `#debug`, je n'ai fait qu'avancer le temps et poser `chute`, `cafe` et `local` (l'équivalent d'une soirée précédente). Désormais, mes clics vérifient que la cible est à l'écran ; sinon, Camille marche d'abord vers elle. Aucune erreur JavaScript. Scripts, journaux et captures : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur4/`.

**Erratum du tour 2** : le bug « la fiche de Mme Delattre se ferme toute seule » venait de mon outil, pas du jeu. Le clic Playwright par sélecteur échouait sur le bouton encore animé. Avec un vrai clic souris sur ses coordonnées, l'option fonctionne (voir plus bas). J'avais déjà retiré pour la même raison mes clics sur le bureau et la rame, tombés hors écran.

## Verdict en 3 lignes

Le jeu se termine enfin pour de vrai. À la vitre, les trois pièces s'enchaînent (refus, aveu, « Mon Dieu. Le frein. »), puis l'écran 21:59:48 pose un vrai dilemme. Les deux fins font mal, chacune à sa façon.
Toutes les corrections que j'ai pu tester tiennent, y compris sur iPhone : gilet jaune, « Filmer quoi ? », fiche d'Odile au coffret et bureau d'Odile.
Le reste est du détail : l'objectif « Ronde » qui s'impose, le compteur « 1 boucles » et un rang très généreux. Il faut maintenant soigner la densité de la première soirée à l'aveugle, plus la mécanique.

## Notes sur 10 (tour 2 → tour 3)

| Critère | T2 | T3 | Pourquoi |
|---|---|---|---|
| Quantité | 8 | **8** | Inchangée ; la route experte occupe chaque tranche de 21:56 à 21:59:02. |
| Fun des actions | 7,5 | **7,5** | Toujours le cadrage et la vitre. La clé avec Mme Delattre reste une fiche à lire, pas un geste. |
| Tension | 7 | **7,5** | Le compte à rebours de la vitre (« il reste 1 min 06 s » → « 0 min 58 s ») et les 4 s par pièce, puis le gyrophare à 21:59:48 : la fin monte bien. |
| Lisibilité | 6,5 | **7** | « Filmer quoi ? » dit au joueur qu'il lui manque un lieu et une heure. Le bureau répond depuis le hall. |
| Conséquences | 8 | **8,5** | Le choix final a un vrai coût dans les deux cas : « Vous irez la voir au parloir » contre « six mois plus tard, la station est rasée ». |
| Rejouabilité | 7 | **7** | Les fins sont définitives, avec le seul bouton « Rejouer depuis le début ». C'est juste, mais rien n'incite à rejouer pour l'autre choix (pas d'indication « 1 fin sur 2 »). |
| Finition / mobile | 6 | **7,5** | Le gilet jaune et la fiche d'Odile au coffret marchent au doigt. Aucune erreur JavaScript sur 6 sessions. |

## Corrections annoncées : vérifiées ou non

| Correction | État | Preuve |
|---|---|---|
| « L'aider à tourner la clé » (21:56:12–21:56:42) | **Vérifiée.** La bulle « Allez… tourne, ma vieille… » apparaît à 21:56:16. La fiche est ouverte à 21:56:18, l'option en tête, et elle reste ouverte. Un vrai clic donne « Vous forcez ensemble. La clé ne tourne pas d'un millimètre. … « Celle d'Henri. Ils ont changé la serrure en 2003… » … la goupille rouge, c'est le cœur du frein. » | `del.js`, `d01_avant_clic.png`, `d02_apres_clic.png` |
| iPhone 13 : gilet jaune à 21:55:02 | **Vérifiée.** Le toucher ouvre « L'HOMME AU GILET JAUNE — Grand, mâchoire serrée… » avec « Le suivre discrètement » et « Lui subtiliser son badge ». La fiche « Sortie » ne s'ouvre plus. | `mob.js`, `m02_tap_gilet.png` |
| Bastien : « Filmer quoi ? » sans l'indice `local` | **Vérifiée** à 21:56:06. Après « Je sais pour le drone », l'option « « Vous pourriez filmer quelque chose pour moi ? » » donne « « Filmer quoi ? » Il hausse les épaules. « Dites-moi où regarder et à quelle heure, et je verrai. D'ici là, je filme la rame. » » | `misc2.log`, `x01_filmer_quoi.png` |
| Plus de petite phrase du gilet jaune à bord | **Vérifiée dans le code seulement** : les petites phrases ne se déclenchent plus quand Camille est à bord (`!S.aboard`). En jeu, mon saut de temps a envoyé directement à l'écran de 22:00:00, donc je n'ai rien observé. | `misc2.log` |
| Fiche d'Odile au coffret (plan B, iPhone) | **Vérifiée** à 21:57:47 : « Votre mère tripote les disjoncteurs du coffret, la lampe de poche entre les dents. », avec l'option « Retourne à ton poste. C'est lui qui a fait sauter le coffret. ». Maman était hors écran ; il faut d'abord marcher vers elle. | `mob2.js`, `m04_odile_coffret.png` |
| Bureau d'Odile depuis le hall (bug 3 du tour 2) | **Vérifié** à 21:58:25 : le SMS « 21h57. Deux minutes. Pas une de plus. — V. » s'affiche, avec « Ouvrir le tiroir » et « Prendre le micro d'annonce ». | `verite.log`, `verite_g06_bureau.png` |
| « C'était qui, au téléphone ? » une seule fois | **Vérifié dans le code seulement** (`!S.said.qTel`). | — |
| L'objectif ne recule plus | **En partie vérifié.** À 21:57:26, après avoir salué maman, l'objectif passe à « Qui s'approche du local technique… » et ne revient plus à « Allez la saluer ». | `misc.log` |
| Filature, étiquette derrière le distributeur | **Pas retestées** dans ce tour (manque de temps). | — |

## Les deux fins (chemin de la résolution, joué de bout en bout)

- **À la vitre**, à 21:58:49, les cartes arrivent dans cet ordre :
  1. 📱 « Les messages signés « V. » » donne « Tu as lu mon téléphone. … Tu n'as rien, Camille. Un papier. » (il reste 1 min 06 s).
  2. ✉️ « L'enveloppe de « V. » » donne « Le prêt pour ton école. Dix-huit mille euros. … « C'était pour toi. » … « Je ne bouge plus d'ici. » ».
  3. 🎥 « La vidéo de Bastien » donne « Mon Dieu. Le frein. … elle pose sa main contre la vitre, sur la vôtre. »
  
  C'est la meilleure minute du jeu. (`verite_g08_carte0..3.png`)
- **21:59:48, écran de choix** : « Votre mère vous tend son téléphone… « Fais ce qui est juste, Camille. Je ne te demanderai rien. » », avec les boutons TOUT DONNER À LA POLICE et EFFACER LES MESSAGES. Le portrait d'Odile, la main sur la bouche, est très juste. (`verite_g09_choix.png`)
- **La vérité** : « Vasseur est arrêté au matin. Votre mère est mise en examen pour complicité. Elle refuse l'avocat. … Vous irez la voir au parloir. Elle vous dira qu'elle dort bien, maintenant. »
- **Le silence** : « La vidéo et l'enveloppe suffiront pour l'homme au gilet jaune. Pas pour Vasseur : six mois plus tard, la station est rasée. … Elle ne prend plus jamais de café à 21 h 57. Vous n'en parlez jamais. »
- Les deux textes sont sobres et sans morale. La dernière phrase du « silence » est excellente.

## Nouveaux bugs et incohérences

1. **Écran de fin : « 1 boucles »** (accord), avec « DÉTECTIVE LÉGENDAIRE » et « 0/6 déductions ». Mes indices étaient posés par `#debug`, mais un joueur peut atteindre la fin en une boucle avec la caméra d'Odile. Le pluriel est faux dans ce cas, et le rang « légendaire » avec 0 déduction sonne creux. (`verite.log`)
2. **L'objectif « Ronde pour votre mère : ☐ bord du quai · ☐ extincteur près du pilier · ☐ porte du local »** s'affiche dès 21:54:16, sans que j'aie accepté quoi que ce soit, et reste jusqu'à 21:57:26 sans jamais cocher une case. Le bug 5 du tour 1 n'est pas corrigé. (`misc.log`, `verite.log`)
3. **Bureau vu de l'extérieur** : à 21:58:25, la fiche « Bureau d'Odile » s'ouvre alors que Camille est dans le hall. Elle lit pourtant le téléphone de maman, posé sur le bureau. Soit Camille entre (ce qui collerait à « Tu as lu mon téléphone. »), soit la fiche doit dire « Vous passez la tête par la porte… ». C'est mineur.
4. **iPhone, plan B** : à 21:57:37, maman est au coffret, hors écran à droite (x = 1334 px). Seule une flèche le signale, et toucher la flèche ne fait rien. Le joueur doit deviner qu'il faut marcher. Je propose qu'un toucher sur la flèche d'un personnage hors champ amène Camille vers lui.

## 6 propositions, classées impact / effort

| # | Quoi | Impact | Effort | ElevenLabs |
|---|---|---|---|---|
| 1 | L'objectif « Ronde » n'apparaît qu'après « D'accord, je fais la ronde » et coche ses cases au fil des vérifications (bug 2). | fort | petit | non |
| 2 | Toucher une flèche de personnage hors champ fait marcher Camille vers lui (mobile surtout, bug 4). | fort (mobile) | petit | non |
| 3 | Écran de fin : « 1 boucle » au singulier, rang calculé aussi sur les déductions, et une ligne « Une autre fin vous attend : effacer / tout donner » pour pousser à rejouer le choix. | moyen | petit | non |
| 4 | « L'aider à tourner la clé » en vrai geste : maintenir pour forcer avec elle, 2 s, la clé résiste, elle rit. Le moment le plus tendre du creux mérite un geste. | moyen | petit | non |
| 5 | Fin « Le silence » : 3 s d'écran de téléphone où l'on glisse pour effacer les messages un par un, au lieu d'un bouton. Le choix deviendrait un geste. | fort | moyen | non |
| 6 | Écran de choix : voix d'Odile pour « Fais ce qui est juste, Camille. Je ne te demanderai rien. » C'est la réplique la plus forte du jeu, elle mérite d'être entendue. | fort | petit | **oui** (une réplique) |

---

# Retours de playtest : Version 11 · tour 2

**Léo · 25 novembre 2026 · environ 25 minutes (5 de plus que prévu)**

J'ai joué la soirée « experte » en 1280×720 avec de vrais clics : sac, drone, cadrage, clé de Mme Delattre, fusible, enveloppe, vidéo et vitre. J'ai aussi testé la cabine en laissant faire le sabotage, puis l'iPhone 13 (gilet jaune, plan B). `#debug` m'a servi à avancer le temps et à poser les indices d'une soirée précédente (`chute`, `cafe`, `local`). Aucune erreur JavaScript. Scripts, journaux et captures : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur3/`.

**Pas atteint** : l'écran de choix et les fins « La vérité » / « Le silence ». Je n'ai eu qu'une pièce d'aveu (l'enveloppe), parce que le bureau d'Odile n'a pas réagi à mon clic à 21:58:32 (voir bug 3). La confrontation s'est donc terminée sur « Le dernier voyage », comme le prévoient les règles.

## Verdict en 3 lignes

Le dernier tiers tient ses promesses. Le cadrage est un vrai geste, la vitre avec son compte à rebours met la pression, l'ordre des pièces compte, et la cabine est une excellente scène de 30 secondes.
Le nouveau contenu du milieu fonctionne : le fusible desserré se voit à 21:56:34. Mais la fiche de Mme Delattre se ferme toute seule au moment où je veux « L'aider à tourner la clé ».
Sur mobile, toucher le gilet jaune ouvre toujours « Sortie ». La priorité aux personnages n'est pas corrigée dans ce cas.

## Notes sur 10 (tour 1 → tour 2)

| Critère | T1 | T2 | Pourquoi |
|---|---|---|---|
| Quantité | 7,5 | **8** | Clé, fusible, cadrage, enveloppe, vidéo et vitre : il se passe quelque chose toutes les 10 à 20 s entre 21:56 et 21:59. |
| Fun des actions | 6,5 | **7,5** | Le cadrage (viseur qui tangue, « 🎯 Bloquer l'angle ») et la cabine (vitre en 2 coups, poignée en 2 tirages) sont de vrais gestes. |
| Tension | 6 | **7** | « Départ à 22:00:00 — il reste 1 min 10 s » et chaque pièce qui coûte 4 s : la vitre fonctionne. |
| Lisibilité | 6,5 | **6,5** | L'option « Filmez… » n'apparaît que si l'on connaît déjà « le local » (soirée précédente), rien ne le signale. Le bureau d'Odile ne répond pas au clic. |
| Conséquences | 7,5 | **8** | Montrer la vidéo trop tôt fait courir maman au frein, et elle se tait : « ce soir, elle ne dira rien d'autre ». On sent que l'erreur vient de moi. |
| Rejouabilité | 6 | **7** | « Le dernier voyage » relance bien : « Mais le gilet jaune ne dira rien. Quelqu'un l'a payé. » |
| Finition / mobile | 6,5 | **6** | Gilet jaune → « Sortie » sur iPhone, fiche de Mme Delattre qui se ferme, clic sur la rame sans effet. |

## Corrections annoncées : vérifiées ou non

| Correction | État | Preuve |
|---|---|---|
| Réplique d'Odile au lieu de « Une seule pièce… » | **Vérifiée.** À 21:58:53, à la vitre : « Et alors ? Ça ne prouve rien. Tu m'espionnes, maintenant ? Tu n'as rien, Camille. Un papier. » | `final2_verite.log`, `g08_carte0.png` |
| Hall assombri pendant le plan B | **Vérifiée** (iPhone, 21:57:35) : le hall passe nettement en veilleuse. | `m03_planB.png` |
| Fusible desserré après 21:56:30 | **Vérifié.** À 21:56:34 : « Un fusible pend à moitié de son support, la vis desserrée. La micro-coupure de tout à l'heure n'était peut-être pas un hasard. » | `g04_fusible.png` |
| « L'aider à tourner la clé » (21:56:12–21:56:42) | **Présente, mais injouable chez moi** : l'option apparaît à 21:56:17, puis la fiche se ferme avant le clic, deux fois de suite (voir bug 1). | `final2_verite.log`, `g01_delattre_cle.png` |
| Priorité aux personnages au toucher (iPhone) | **Non corrigée.** À 21:55:02, toucher le gilet jaune (debout devant la porte d'entrée) ouvre « SORTIE — Partir ? Non. Pas tant que cette rame n'est pas partie. » | `m01_t62.png`, `m02_tap_gilet.png` |
| Fiche d'Odile au coffret (plan B) | **Non vérifiée** : sur iPhone, maman était hors champ (il n'y avait qu'une flèche « Odile »). Mon toucher n'a rien ouvert. | `m03_planB.png` |
| Filature (15 s et un regard esquivé), objectif qui ne recule plus, « C'était qui, au téléphone ? » posée une fois, étiquette derrière le distributeur | **Pas retestées** dans ce tour. | — |

## Ce qui marche (vérifié en jouant)

- **Cadrage** : à 21:56:04, « Je sais pour le drone » donne « Il pâlit. « Vous allez me dénoncer ? Je veux juste filmer le dernier voyage. C'est tout ce qui me reste de lui. » ». J'ai bloqué l'angle avec un décalage de 10,9 px, ce qui donne « Pile sur la porte, et à hauteur de visage. Joli. » À 21:58:42, la vidéo : « on voit son visage, net, quand il arrache quelque chose dans l'armoire ». (`f02_cadrage.png`, `f03_cadre_ok.png`)
- **Enveloppe** : la sacoche est posée à 21:57:38, l'enveloppe prise à 21:57:40 et confiée à Mme Delattre (« Personne ne fouille le sac d'une vieille dame. »), puis rendue à 21:58:44 (« Tenez. Faites-en bon usage. »).
- **Vitre** : ouverte à 21:58:49, avec « il reste 1 min 10 s ». La vidéo montrée sans aveux donne « Le frein ! … ce soir, elle ne dira rien d'autre », puis la fin « Le dernier voyage ». C'est conforme à la résolution.
- **Cabine** : je laisse faire le sabotage, je monte à bord à 21:59:10 et le bandeau « ANNONCE ▸ « Prenez place. Départ imminent. » » s'affiche. Ensuite, 30 s : l'interphone coûte 5 s (« Le frein ne répond plus d'ici ! La poignée rouge, Camille ! »), puis rassurer, 2 coups, 2 tirages. Fin « À LA MAIN ». La voix de maman dans l'interphone fonctionne très bien en émotion. (`c01_abord.png`, `cab.log`)
- **Règle d'or** respectée : aucune mention de boucle avant la première fin.

## Nouveaux bugs et incohérences

1. **La fiche de Mme Delattre se ferme toute seule** à 21:56:17. Sa fiche s'ouvre avec « L'aider à tourner la clé » en tête de liste. Moins d'une seconde après, le canvas reprend les clics : la fiche s'est fermée. J'ai rouvert la fiche et le résultat a été le même. Je soupçonne la bulle « Allez… tourne, ma vieille… » ou son déplacement vers la porte. Conséquence : le nouveau contenu du creux est inaccessible si l'on ne clique pas en moins d'une seconde. (`final2_verite.log`, `g01_delattre_cle.png`)
2. **iPhone : gilet jaune → « Sortie »** à 21:55:02, alors qu'il se tient devant la porte d'entrée. Le personnage devrait passer avant la sortie. (`m02_tap_gilet.png`)
3. **Bureau d'Odile sans réaction** à 21:58:32. Le sabotage est fini, maman n'est pas revenue, je clique sur le bureau à travers la vitre, et rien ne s'ouvre. Le point d'intérêt n'est actif que si l'on est déjà *dans* la cabine (`hotOn` : `desk`). Résultat : pas de « messages signés V. », donc pas de deuxième pièce, et pas de fin à choix. Un joueur ne comprend pas pourquoi le bureau ne répond pas. Il faudrait au minimum une fiche « Il faut entrer dans la cabine pour fouiller le bureau ».
4. **Clic sur la rame sans effet** à 21:59:10 en 1280×720, depuis le hall : aucune fiche ne s'ouvre, j'ai dû passer par `examine('rame')`. Peut-être un point d'intérêt hors de l'écran ou masqué ; c'est à vérifier.
5. **Ce qui débloque « Filmez la porte… » n'est expliqué nulle part** : sans l'indice `local` (soirée précédente), Bastien ne propose que « Partir » après « Je sais pour le drone ». Rien ne dit au joueur qu'il lui manque quelque chose. Je propose une réplique de Bastien : « Filmer quoi ? Je ne sais même pas ce que je cherche. »
6. **À bord, à 21:59:55**, la caméra reste sur le hall, où le gilet jaune lance « Toujours pas parti, vous ? ». Comme Camille est déjà assise dans la rame, on croit qu'il s'adresse à elle. (`c01_abord.png`)

## 8 propositions, classées impact / effort

| # | Quoi | Impact | Effort | ElevenLabs |
|---|---|---|---|---|
| 1 | Empêcher une bulle ou un déplacement de PNJ de fermer une fiche ouverte (bug 1). | fort | petit | non |
| 2 | Bureau vu de l'extérieur : fiche « Entrez dans la cabine pour fouiller » au lieu d'un clic muet (bug 3). | fort | petit | non |
| 3 | iPhone : le personnage avant « Sortie », ou un mini-menu « Gilet jaune / Sortie » (bug 2). | fort | petit | non |
| 4 | Réplique de Bastien quand le joueur ne connaît pas encore « le local » : « Filmer quoi ? » (bug 5). | moyen | petit | non (texte) ; **oui** pour la voix |
| 5 | À la vitre, si le joueur n'a qu'une pièce, afficher une carte grisée « Il te manque quelque chose… » plutôt que de laisser jouer la vidéo et de tout perdre. | moyen | petit | non |
| 6 | À bord, cadrer la rame (ou afficher une vignette « Vous êtes assise ») pour que « Toujours pas parti, vous ? » ne vise plus Camille. | moyen | moyen | non |
| 7 | Cabine : 1 s de bruit de vitre et un flash rouge à chaque action, pour que les 30 s se jouent au réflexe plutôt qu'en lisant. | moyen | moyen | non |
| 8 | Fin « Le dernier voyage » : une réplique d'Odile au frein (« Pardon, ma chérie ») enregistrée, pour l'émotion. | moyen | moyen | **oui** (une réplique) |

---

# Retours de playtest : version 11 (session courte)

**Léo · 25 novembre 2026 · environ 20 minutes de test**

Ce que j'ai fait : une soirée à l'aveugle en 1280×720 (vrais clics, temps avancé avec `#debug` dans les creux), puis des tests ciblés avec `#debug` (badge avant et pendant l'appel, filature, téléphone de service, plan B, fin de boucle), et un rapide passage sur iPhone 13. Aucune erreur JavaScript. Scripts, journaux et captures : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur2/`.

**Non vérifié en jeu, faute de temps** : le cadrage avec Bastien, la confrontation à la vitre, la cabine (interphone, bandeau pendant la chute), l'image de l'écran de choix et les fins illustrées. Pour ces points, j'ai seulement lu le code. Je ne les compte ni comme corrigés ni comme cassés.

## Verdict en 3 lignes

Le jeu ne se désamorce plus tout seul. Une seule preuve ne fait plus avouer maman, et si elle reste à son poste, Vasseur passe au plan B : le coffret du hall saute à 21:57:29.
Les nouveaux gestes (badge maintenu, filature) sont de vrais gestes. Mais la filature se gagne en 9 secondes sans rien faire, et le badge est généreux.
La soirée à l'aveugle reste creuse entre 21:55:45 et 21:57:10, et on retrouve des personnages masqués par un point d'intérêt (sur mobile, cette fois).

## Notes sur 10 (avant → maintenant)

| Critère | Avant | V11 | Pourquoi |
|---|---|---|---|
| Quantité | 7 | **7,5** | Le plan B et la filature ajoutent du jeu au milieu. Le trou 21:55:45 → 21:57:10 existe encore à l'aveugle. |
| Fun des actions | 5 | **6,5** | Le badge est devenu un geste de timing (maintenir, lâcher sur « ⚠️ Il bouge la tête… »). La filature et son bouton « 👀 Regarder ailleurs » sont une bonne idée, mais trop courtes. |
| Tension | 5 | **6** | Le coffret qui saute, la sonnerie de défaut et maman qui quitte sa vitre : enfin une menace qui s'adapte à moi. |
| Lisibilité | 6 | **6,5** | « Il faudrait qu'il soit occupé ailleurs » et « Une seule pièce ne la fera pas parler » guident bien. Mais l'objectif recule parfois (voir bug 3). |
| Conséquences | 6 | **7,5** | Les aveux en deux temps et le plan B transforment mes bonnes actions en relances, au lieu de clore la soirée. |
| Rejouabilité | 6 | **6** | Pas retesté (fins, « Continuer la nuit »). |
| Finition / mobile | 7 | **6,5** | Le bouton du badge fait 259×39 px, c'est correct. Mais sur iPhone, toucher le gilet jaune ouvre « Sortie ». |

## Frustrations du premier rapport : corrigées ou non

| # | Frustration | État | Preuve |
|---|---|---|---|
| 1 | Aveux en 95 s avec le seul reçu | **Corrigé.** À 21:57:13 : « Je lui ai ouvert. Il devait juste regarder. » Puis : « Et alors ? Ça ne prouve rien. Tu m'espionnes, maintenant ? » La dernière phrase, « Une seule pièce ne la fera pas parler. Il en faudrait une deuxième. », est trop explicite : c'est le jeu qui parle, pas Odile. | `b3.log` |
| 2 | Pas de plan B | **Corrigé.** À 21:57:16 : « Elle ne bouge pas de sa cabine. … Le coffret ? D'accord. » À 21:57:29 : « Une sonnerie de défaut retentit. Le tableau des départs s'éteint. » L'option « Retourne à ton poste. C'est lui qui a fait sauter le coffret. » est bien proposée. | `t2_04_pb206.png`, `t2_08_pb_odile.png` |
| 3 | Piste figée | **Non vérifié en profondeur.** En début de boucle 2, sans avoir vu la chute : « Observez. Que se passe-t-il à 22 h 00 ? » (c'est normal à ce stade). | `e1_*_loop2.png` |
| 4 | Badge puni avant la fenêtre | **Corrigé.** À 21:54:30 et à 21:54:53 : « Mais il vous fait face, les yeux sur vous : impossible. Il faudrait qu'il soit occupé ailleurs. » Aucune méfiance. Pendant l'appel (21:55:00), le geste est maintenu et réussi en relâchant sur l'alerte. | `t1_01_badge_t30.png`, `t2_01_badge_game.png`, `t2_02_badge_res.png` |
| 5 | Trous dans la partie experte | **En partie corrigé** : il y a la filature (21:55:35) et le plan B (21:57:16 → 21:58:20). À l'aveugle, de 21:55:45 à 21:57:10, je n'ai rien vu de neuf à part « Oh ! Les lumières… » à 21:56:40. | `b3.log` |
| 6 | Dénouement en fiche | Le code d'une confrontation « À la vitre », avec ses pièces et un compte à rebours, existe. **Je ne l'ai pas joué.** | — |
| 7 | Micro dans la pièce du gilet jaune | **Corrigé.** Le téléphone de service propose « Composer le 9 (sonorisation) ». Sans connaître le nom du gilet jaune, la seule annonce est « Le dernier départ est retardé. » | `t1_07_phone9.png` |
| 8 | Personnages qui masquent les points d'intérêt | **Pas entièrement corrigé.** (a) iPhone 13, 21:55:02 : je touche le gilet jaune et la fiche « SORTIE — Partir ? Non. » s'ouvre. (b) 1280×720, 21:55:01 : en visant Odile, j'ouvre Bastien. | `m1.js`, `b3.log` |

## Nouveaux bugs et incohérences

1. **Plan B, 21:57:55** : maman est au coffret du hall, mais sa fiche dit « Votre mère attend que le gobelet se remplisse. Elle ne vous a pas entendu approcher. » C'est le texte de la pause café, alors qu'elle n'est pas au distributeur. Capture : `t2_08_pb_odile.png`.
2. **Filature trop facile** : je lance « Le suivre discrètement » à 21:55:35, et à 21:55:45 c'est gagné (« Nouvel indice noté dans le carnet », `tailWon: true`, 0 repérage), sans avoir eu à utiliser « Regarder ailleurs ». Dix secondes, aucune tension. Capture : `t1_05_follow_after.png`.
3. **L'objectif recule.** À 21:57:20 (maman part au café), la ligne d'objectif « Ronde pour votre mère : ✓ bord du quai · ☐ extincteur · ✓ porte du local » redevient « Votre mère fait partir la dernière rame à 22 h 00. Allez la saluer, puis montez à bord. », alors que je l'ai saluée trois minutes plus tôt. Source : `b3.log`.
4. **Option qui reste après réponse** : « C'était qui, au téléphone ? » reste proposée après « Personne. Un collègue. » (21:57:13). Source : `b3.log`.
5. **La ronde démarre sans que je la propose** : à 21:54:19 (et à 21:54:30 sans avoir parlé à personne), l'objectif devient « Ronde pour votre mère… ». La bulle « Camille ! Tu fais la ronde pour moi ? » arrive ensuite, mais je n'ai rien accepté.
6. **Étiquette orpheline** : à 21:59:59, l'étiquette « Bastien » flotte à côté du distributeur de boissons, sans personnage visible (il est caché derrière la machine). Capture : `b3_19_fin2.png`.
7. **Bulle sur flèche hors champ** : à 21:55:00, la bulle « Ils vont tout raser, de toute façon. » recouvre la flèche « Odile ». Capture : `b3_02_gerald_after.png`.
8. **Plan B, lumière** : le coffret du hall saute, mais à l'image le hall reste éclairé comme avant. Seul le toast le dit. Capture : `t2_07_pb232.png`.

À noter (artefact de débogage, pas un bug) : si je saute directement à 21:59:52, la fin dit « Rien ne se passe… Empêcher le sabotage ne suffit pas », alors que je n'ai rien empêché. C'est dû au saut de temps, un joueur ne le verra pas.

**Règle d'or** : respectée. Rien ne parle de boucle avant l'écran « 22:00:00 … Le monde se replie sur lui-même. »

## 8 propositions, de la plus rentable à la plus chère

| # | Quoi | Impact | Effort | ElevenLabs |
|---|---|---|---|---|
| 1 | Corriger la fiche d'Odile pendant le plan B, par exemple : « Votre mère tripote les disjoncteurs du coffret, lampe de poche entre les dents. » | moyen | petit | non |
| 2 | Filature : exiger au moins un regard bien esquivé (ou 20 s de suivi) avant de la gagner. Aujourd'hui, elle est gagnée avant le premier regard. | fort | petit | non |
| 3 | Plan B visible : assombrir le hall et faire clignoter le néon pendant la coupure. | fort | petit | non |
| 4 | Ne plus faire reculer l'objectif : garder « Saluée ✓ » quand maman quitte sa cabine. Retirer « C'était qui, au téléphone ? » une fois la réponse donnée. | moyen | petit | non |
| 5 | Remplacer « Une seule pièce ne la fera pas parler. Il en faudrait une deuxième. » par une réplique d'Odile (« Tu n'as rien, Camille. Rien. »), et laisser l'onglet Déductions faire l'indication. | moyen | petit | non (texte) ; **oui** s'il faut la voix |
| 6 | Priorité au personnage sur la « Sortie » quand on touche l'écran (iPhone), ou petit menu « Gilet jaune / Sortie ». | fort (mobile) | petit | non |
| 7 | Remplir 21:55:45 → 21:57:10 à l'aveugle : la scène de Mme Delattre et de la clé d'Henri (déjà proposée), ou faire de « Oh ! Les lumières… » (21:56:40) un vrai indice cliquable sur le coffret. | fort | moyen | non (texte) ; **oui** pour une voix de Mme Delattre |
| 8 | Plan B : faire sonner le téléphone de service pour Odile (voix de Vasseur), à décrocher avant elle. | fort | moyen | **oui** (une réplique de Vasseur) |

---

# Retours de playtest : « La Dernière Rame » (2D, version 18)

**Léo, 27 ans, joueur exigeant · 25 novembre 2026**

Ce que j'ai joué (Playwright, vrais clics sur le canvas, 1280×720 puis iPhone 13) :

- **Soirée 1 à l'aveugle, avec le tutoriel**, en temps réel. Fin « aveux sans preuve ».
- **Soirée 2 à l'aveugle**, pour comprendre (jouée deux fois, parce que mes premiers clics tombaient hors champ). Fin « sursis ».
- **Lecture de `RESOLUTION.html`**.
- **Soirées 3 et 4**, en visant la vraie fin. La 3 a échoué à cause du badge. La 4 aboutit à **La vérité**, puis je l'ai rejouée pour voir **Le silence**.
- **Sessions courtes avec `#debug`** pour tester chaque mini-jeu : cadenas, photo, cachette avec souffle et photo du sabotage, armoire, micro, cabine « À la main ».
- **Passage iPhone 13**.

Aucune erreur JavaScript en console sur toutes les sessions. Captures et scripts : `/tmp/claude-0/-workspace/00b10ced-f74c-50ab-a547-eda9febf00db/scratchpad/joueur/`.

---

## 1. Verdict en 3 lignes

C'est **magnifique** : les illustrations, la pluie, l'écriture et les petits détails qui serrent le cœur (l'extincteur révisé par « H. Delattre », le gant offert pour leurs trente ans, la clé d'Henri essayée « tous les 4 février »).

Mais **le jeu se désamorce tout seul**. Les gestes les plus naturels, ramasser la pièce pour offrir un café ou imprimer le reçu puis le dire à maman, **terminent l'intrigue en 95 secondes** et me laissent 4 min 25 s sans rien faire.

En partie experte, le cœur de la soirée (21:55:30 → 21:57:15) est un tunnel d'attente, et **le dénouement tient en trois boutons dans une seule fiche**, au distributeur de boissons.

---

## 2. Notes sur 10

| Critère | Note | Pourquoi |
|---|---|---|
| Quantité de choses à faire | **7** | 23 points d'intérêt, 4 personnages bavards, 5 souvenirs, 13 fins. Mais tout se tasse entre 21:54:00 et 21:55:40, puis plus rien de neuf jusqu'à 21:57:15 dès la 2ᵉ soirée. |
| Fun des actions | **5** | Quatre vrais gestes : la photo à cadrer, le levier à maintenir, le souffle à retenir, et la cabine (mais celle-ci n'est qu'une suite de boutons). Le cadenas se résout du premier coup (1989 est écrit partout). Le reste, c'est cliquer puis lire. |
| Tension | **5** | La cachette est le meilleur moment du jeu. La soirée 1, elle, n'a aucune tension (je l'ai « gagnée » sans le savoir à 21:55:34), et le climax se joue dans une fiche, sans mise en scène. |
| Lisibilité | **6** | Les étiquettes, les flèches hors champ et la ronde sont très bien. En revanche, la piste reste bloquée sur « Observez. Que se passe-t-il à 22 h 00 ? » tant qu'on n'a pas vu la chute, les personnages masquent des points d'intérêt, et la fenêtre du badge est invisible. |
| Conséquences des choix | **6** | Les personnages réagissent : Odile reste, Mme Delattre rit de mon accusation, le gilet jaune se méfie. Mais presque toutes les conséquences ont le même effet : **la soirée s'arrête d'être intéressante**. |
| Rejouabilité | **6** | Les déjà-vu à partir de la boucle 3 sont formidables, et il y a 16 succès et 13 fins. Mais j'ai eu la vraie fin à la boucle 4 sans toucher une seule déduction (0/6), puis le seul bouton proposé est « Rejouer depuis le début ». |
| Finition / mobile | **7** | Très beau, aucune erreur JS, jouable au doigt (boutons du HUD 44×40 px). Quelques textes obsolètes, des étiquettes qui se chevauchent et une illustration réutilisée (voir §5). |

---

## 3. Chronologie d'une soirée, par tranches de 30 s

Densité : 0 = rien, 3 = trop de choses à la fois. Deux colonnes de densité : **S1** = ma première soirée à l'aveugle ; **Exp.** = une soirée de joueur qui connaît déjà les indices (boucle 3 ou plus).

| Tranche | Ce qu'on peut faire | S1 | Exp. | Temps mort ? | Je m'ennuyais ? |
|---|---|---|---|---|---|
| 21:54:00–21:54:30 | Tutoriel (sol, personne, point lumineux). Odile fait signe (21:54:05), puis demande la ronde (21:54:12). Annonce (21:54:20). Banc (pièce, registre), affiche (souvenir), distributeur (reçu, ticket inaugural), Mme Delattre (4 répliques et la photo). | 3 | 3 | non | Non. Ça déborde. J'ai le reçu à 21:54:05 et la pièce à 21:54:08 : trop puissants, trop tôt. |
| 21:54:30–21:55:00 | Mme Delattre perd son gant (21:54:44). Le gilet jaune entre (21:54:48), puis téléphone (21:54:52 à 21:55:22). Vol du badge, à partir de 21:54:56 seulement. Fin de la ronde. Goupille par terre. | 3 | 3 | non | Non. C'est la meilleure minute du jeu. |
| 21:55:00–21:55:30 | Hélice tombée de la poche de Bastien (21:55:02). Essai de drone (21:55:24 à 21:55:36). Téléphone du guichet (21:55:30). Poubelle (journal), téléphone de service. | 3 | 2 | non | Non. |
| 21:55:30–21:56:00 | Carte de Vasseur sous les gobelets (après 21:55:35). Le gilet jaune observe le local (21:55:44). Odile vérifie ses voyants. Écrans (caméra coupée). | 2 | 1 | à moitié | S1 : **oui**. Odile venait d'avouer et de boire mon café, donc il n'y avait plus d'enjeu. |
| 21:56:00–21:56:30 | Bastien pose son sac (drone, pince). Mme Delattre essaie sa clé (21:56:12 à 21:56:42). | 1 | 1 | **oui** | Oui. En boucle 3+, je connais déjà les deux, je clique sur « Attendre ». |
| 21:56:30–21:57:00 | Micro-coupure de lumière (21:56:30) et réplique sur le coffret. Coffret électrique. | 0 | 1 | **oui** | Oui, 30 s pour une réplique d'ambiance. |
| 21:57:00–21:57:30 | SMS de V. (21:57:15). Odile part au café (21:57:20). Local vide : bureau, tiroir, casier, micro, recoin. | 0 | 3 | S1 : oui | S1 : oui (Odile restait). Exp. : non, la fenêtre fait 10 s et elle est tendue. |
| 21:57:30–21:58:00 | Sabotage (40 s de travail). Sacoche et enveloppe. Cachette (souffle, photo). Porte coincée, lumière. | 0 | 3 | S1 : oui | Exp. : non, c'est le pic. Il faut choisir une seule action. |
| 21:58:00–21:58:30 | Annonce (21:58:00). Confier l'enveloppe à Mme Delattre. Le gilet jaune cherche son enveloppe (21:58:20). Armoire, si on a la goupille. | 0 | 2 | S1 : oui | Exp. : un peu (j'ai attendu 62 s entre l'enveloppe confiée et 21:58:43). |
| 21:58:30–21:59:00 | Odile revient (21:58:40). Vidéo de Bastien. Récupérer l'enveloppe. Aveux et preuves. | 0 | 3 | S1 : oui | Exp. : non, mais tout se règle en 3 clics. |
| 21:59:00–21:59:30 | La rame est à quai (21:59:00) : carte postale sous les sièges, monter à bord. Mme Delattre salue la rame. | 1 | 2 | non | Non, la carte postale est jolie. |
| 21:59:30–22:00:00 | Berline de Vasseur (21:59:30, seulement si on a l'enveloppe). Choix final (21:59:48). Fin. | 0 | 2 | S1 : oui | S1 : oui. J'ai regardé l'horloge pendant 4 min 25 s. |

**Bilan.** En soirée 1, j'ai eu **26 secondes de jeu dense, suivies de 265 secondes de vide** : une fois le café offert et les aveux obtenus, plus rien ne bouge. En soirée experte, il y a deux trous de 30 à 100 s (21:55:40 → 21:57:15 et 21:57:45 → 21:58:40) que le bouton « Attendre » cache sans les combler.

---

## 4. Mes 10 plus grosses frustrations

1. **J'ai « fini » l'histoire en 95 secondes, à l'aveugle.**
   - Ce que j'ai fait : soirée 1. À 21:54:05, j'imprime l'historique du distributeur. À 21:54:56, j'achète un café avec la pièce du banc. À 21:55:34, je vais voir maman.
   - Ce qui s'est passé : on me propose « À 21 h 31, c'est toi qui lui as ouvert le local. ». Odile avoue tout (le prêt, les 18 000 €, Vasseur) et prend le café : « Bon. Je reste là, alors. » Ensuite, plus rien jusqu'à 22:00:00. La fin dit : « Sans preuve du sabotage, Vasseur ne sera jamais inquiété. »
   - Ce que j'attendais : qu'elle nie, qu'elle se fâche, qu'elle me demande où j'ai eu ça. Le plus gros secret du jeu tombe à la 94ᵉ seconde de la première soirée, sur un simple reçu.

2. **Le café et les aveux tuent la soirée au lieu de la relancer.**
   - Ce qui s'est passé : quand Odile ne part pas, le gilet jaune « a attendu, puis il est parti ». Pas de plan B, pas de nouvelle menace.
   - Ce que j'attendais : que Vasseur s'adapte, par exemple avec un coup de fil pour faire sortir Odile ou une diversion. Mes bonnes actions devraient ouvrir du jeu, pas le fermer.

3. **La piste m'a laissé tomber pendant trois soirées.**
   - Ce qui s'est passé : de la boucle 2 jusqu'à l'écran de la vraie fin, elle affiche « Observez. Que se passe-t-il à 22 h 00 ? ». Je n'ai jamais vu la chute, parce que j'empêchais le sabotage sans le vouloir. Elle ne se débloque qu'avec l'indice « chute ». Capture : `m1_02_attendre.png`.
   - Ce que j'attendais : qu'elle tienne compte de ce que je sais réellement (j'avais 26 indices).

4. **Le vol du badge m'a coûté la soirée 3.**
   - Ce que j'ai fait : à 21:54:55, l'option « Lui subtiliser son badge » est déjà proposée, je la prends.
   - Ce qui s'est passé : je l'essaie à 21:54:55,7, mais la fenêtre ne commence qu'à 21:54:56. Réponse : « Sa main se referme sur votre poignet. "Bas les pattes." ». Le gilet jaune se méfie, quitte la station, et à 21:58:43 la vidéo de Bastien est vide : « Rien. Une porte. »
   - Ce que j'attendais : soit pas d'option avant la fenêtre, soit un signe clair (« Il n'a pas encore décroché »).

5. **Dans le chemin optimal, j'attends plus que je ne joue.**
   - Ce qui s'est passé : en soirée 4, j'ai fini mes actions utiles à 21:55:28 et il ne se passe rien d'utile jusqu'à 21:57:15 (104 s). Rebelote de 21:57:41 (enveloppe confiée) à 21:58:43 (62 s).
   - Ce que j'attendais : des choses à faire pendant ces trous. Le bouton « Attendre » est un aveu.

6. **Le dénouement est une fiche de dialogue.**
   - Ce qui s'est passé : à 21:58:47, Odile est au distributeur de boissons (« Votre mère attend que le gobelet se remplisse »). Je clique « Qui t'écrit, signé V. ? », puis « Lui montrer l'enveloppe de « V. » », puis « Lui montrer la vidéo de Bastien ». Tout se passe à la même seconde, dans la même fiche.
   - Ce que j'attendais : une confrontation à la vitre, avec un rythme, un risque (le mauvais ordre la ferme) et un gros plan. Le choix final (21:59:48), lui, est très réussi.

7. **Le micro d'annonce sabote son propre usage.**
   - Ce que j'ai fait : il est sur le bureau d'Odile, dans la même pièce que l'armoire. Pour appeler « M. Gérald Morin » pendant le sabotage, je suis entré à 21:57:50.
   - Ce qui s'est passé : il a crié « Dégagez de là ! » et il est parti. C'est mon irruption qui l'a arrêté, pas l'annonce. J'ai quand même eu le succès « Au micro ».
   - Ce que j'attendais : pouvoir faire l'annonce de l'extérieur, et un succès qui récompense ce que l'annonce a réellement provoqué.

8. **Les personnages masquent les points d'intérêt.**
   - Ce qui s'est passé : à 21:55:20, je clique sur la porte du local et j'ouvre le dialogue de Bastien, planté devant. Même chose pour le casier d'Odile, qui ouvre Bastien ou Odile (trois fois au total).
   - Ce que j'attendais : que le point visé l'emporte, ou un petit menu « Porte / Bastien ».

9. **La cachette, c'est beaucoup d'attente passive.**
   - Ce qui s'est passé : entré trop tôt, je reste 50 s sur « Vous êtes caché derrière les armoires. Le temps passe… ». Après le sabotage, rebelote jusqu'à ce qu'Odile me trouve à 21:58:48 (« Camille ?! Qu'est-ce que tu fais là-dedans ? Sors ! »). Le signal « Il va se retourner… » n'est qu'une petite ligne grise au-dessus des boutons (`m1_09_souffle.png`).
   - Ce que j'attendais : un signe à l'image (sa tête qui pivote, un battement), et pouvoir m'éclipser quand il a le dos tourné.

10. **Rien ne me pousse à rejouer après la vraie fin.**
    - Ce qui s'est passé : 0/6 déductions et j'ai quand même gagné, donc l'onglet « Déductions » n'a servi à rien. Puis l'écran de fin n'a qu'un bouton : « Rejouer depuis le début ».
    - Ce que j'attendais : un « Continuer la nuit » pour aller chercher les 10 fins et le souvenir qui me manquaient.

Petites frustrations en plus :

- Le cadenas est trivial. « Depuis le premier jour », l'affiche, le ticket et Mme Delattre disent tous 1989, et j'ai ouvert au premier essai sérieux.
- La cabine « À la main » se résume à « bouton, bouton, bouton ». Le « Tirer plus fort » aurait dû être un appui maintenu, comme le levier de l'armoire.

---

## 5. Incohérences et bugs relevés

| # | Où / quand | Constat | Preuve |
|---|---|---|---|
| 1 | Toast « Près de l'entrée, l'homme au gilet jaune sort son téléphone et baisse la voix. » | Il s'affiche **bien après la fin de l'appel** (fini à 21:55:22) : vu à 21:55:28, 21:55:47, et surtout à 21:57:22 et 21:59:04 après un « Attendre », alors que le gilet jaune est dans le local ou sur le quai. Constaté dans 5 sessions. | journaux `s3.log`, `m1.log`, `m2.log`, `m3b.log`, `mob.log` |
| 2 | Cabine, interphone (vers 22:00:05) | « Au poste, votre mère n'est pas encore revenue de son café. » Or elle est revenue à 21:58:40. | `m3b.log`, `m3b_0*_cabine.png` |
| 3 | Cabine | Le bandeau « ANNONCE ▸ « Prenez place. Départ imminent. » » reste affiché pendant la scène de la chute. | `m3b_04_cabine.png` |
| 4 | Fin « Le silence » | Elle réutilise l'illustration de l'écran de choix (le téléphone à la vitre). C'est la seule fin vue sans illustration propre. | `s3c_v_28_overlay.png` et `s3c_s_31_epilogue.png` |
| 5 | Écran final (La vérité / Le silence), 1280×720 | Le bas du panneau est coupé : la 3ᵉ ligne d'épilogue (« Gérald… ») est tronquée par le bord. Je n'ai pas vérifié si le panneau défile. | `s3c_v_31_epilogue.png` |
| 6 | Micro d'annonce | Le succès « Au micro » est accordé alors que c'est mon irruption dans la cabine qui a fait fuir le gilet jaune (« Dégagez de là ! » à 21:57:50, puis l'annonce à 21:57:51). | `m2.log` |
| 7 | Vol du badge | L'option est proposée avant la fenêtre de réussite. Essai à 21:54:55,7 = échec garanti, avec méfiance à la clé. | `s3.log` |
| 8 | Étiquettes | « Écrans de surveillance » chevauche « Odile ». Les flèches hors champ « Odile » et « Bastien » se superposent en haut de l'écran (« OdBasti… »). | `s1_33_t_215722.png`, `s2_05_photo_mg.png` |
| 9 | Mini-jeu photo, 1280×720 | Le bouton « Partir » est poussé sous le bord de l'écran. | `s2_05_photo_mg.png` |
| 10 | Points d'intérêt | La porte du local et le casier sont masqués par un personnage debout devant : le clic ouvre le personnage. | `s1.log` (21:55:20), `s2.log`, `s2b.log` |
| 11 | Fin « aveux » en soirée 1 | « Le gilet jaune a attendu, puis il est parti », alors qu'à 21:59:09 il est encore planté sur le quai. Mineur. | `s1_40_t_215909.png` |
| 12 | Documentation | `RESOLUTION.html` s'annonce « version 10 », avec « 12 succès » et « 3 premières minutes » à jour ; le jeu est en version 18 et affiche 16 succès (« 5/16 succès »). | `s3c_v_31_epilogue.png` |

**Règle d'or (boucle révélée trop tôt) : respectée.** Avant la première fin, rien ne parle de boucle. Le « Tout recommence. » n'arrive que sur l'écran de fin, et les déjà-vu (« J'ai rêvé de cette soirée. Plusieurs fois. », « Bizarre… Je savais que vous alliez passer là. ») seulement à partir de la boucle 3. C'est très bien dosé.

---

## 6. Dix propositions de contenu, de la plus rentable à la plus chère

| # | Quoi | Heure | Geste pour le joueur | Ce que ça sert | Coût | Crédits ElevenLabs |
|---|---|---|---|---|---|---|
| 1 | **Plan B de Vasseur quand Odile reste.** Si le café est offert, les aveux obtenus ou le badge montré, Vasseur appelle le téléphone de service : « Odile, un problème au guichet du haut, vite. » Ou le gilet jaune coupe le coffret du hall pour la faire sortir. | 21:57:10–21:57:30 | Empêcher Odile de partir (décrocher avant elle, se poster devant la porte) ou suivre le gilet jaune dans le noir. | Les bonnes actions relancent la soirée au lieu de la clore. Cela corrige les frustrations 1 et 2. | moyen | non (textes) |
| 2 | **Aveux en deux temps.** Avant 21:57:15, une seule pièce fait seulement fermer Odile (« Tu m'espionnes, maintenant ? »). Il faut deux pièces (reçu et SMS, ou reçu et casier) pour l'aveu complet. | toute la soirée | Choisir et combiner ses preuves. Un mauvais choix lui fait perdre de l'humeur. | La révélation se mérite et ne tombe plus à 21:55:34 en soirée 1. Cela donne enfin un rôle aux déductions. | petit | non |
| 3 | **Piste qui suit le savoir réel.** Si le joueur a empêché le sabotage deux fois sans voir la chute, la piste dit : « Vous avez empêché quelque chose. Quoi ? Laissez-le faire, une fois. » | début de boucle | Aucun, c'est du guidage. | Lisibilité (frustration 3). | petit | non |
| 4 | **Délier l'option du badge de sa fenêtre.** Avant 21:54:56, afficher « Il n'a pas encore décroché… » (grisé). Pendant l'appel, un mini-geste : une barre qui se remplit pendant qu'il parle, et relâcher avant qu'il ne raccroche. | 21:54:48–21:55:22 | Un pickpocket en timing. | Un geste de plus dans la meilleure minute, et plus d'échec injuste. | petit | non |
| 5 | **Remplir 21:55:40–21:57:15 avec une filature.** À 21:55:44, le gilet jaune repère le local. On peut le suivre de loin avec une jauge « trop près / trop loin » : réussir révèle le code du local ou son heure exacte, rater le rend méfiant. | 21:55:44–21:56:40 | Marcher à bonne distance (la règle des 7 s existe déjà, elle serait enfin lisible). | Tension et enquête dans le trou du milieu. | moyen | non |
| 6 | **Cadrer avec Bastien.** S'il accepte de filmer, on règle avec lui l'angle de la caméra (réutiliser le viseur tremblé du mini-jeu photo, mais sur la porte du local). Selon le cadrage, la vidéo montre la main, le visage, ou rien. | 21:56:00–21:57:15 | Cadrer. | Un geste dans le trou du milieu, et une preuve qui dépend du talent du joueur. | petit à moyen | non |
| 7 | **Mettre en scène la confrontation finale.** À la vitre (et pas au distributeur), en trois temps avec gros plans : l'aveu, puis l'enveloppe, puis la vidéo. L'ordre compte : la vidéo d'abord la fait courir au frein avant d'avoir avoué. Réutiliser les voix déjà enregistrées : « Il m'a menti. », « Mon Dieu. Le frein. », « Fais ce qui est juste ». | 21:58:40–21:59:40 | Choisir l'ordre, sous la pression de l'horloge. | Un vrai climax (frustration 6). | moyen | non (voix existantes) |
| 8 | **Micro accessible depuis le hall.** Une borne « APPEL VOYAGEURS » près du guichet, ou l'annonce faite par Odile à la vitre si on la convainc. Le succès n'est accordé que si l'annonce a fait bouger le gilet jaune. | 21:57:20–21:58:40 | Faire diversion à distance. | Rend la stratégie réellement utilisable (frustration 7). | petit | non |
| 9 | **Cachette vivante.** Sa tête pivote à l'image, une vignette rouge et un battement s'ajoutent au texte. On peut sortir discrètement quand il a le dos tourné. Un bouton « Attendre » dans la cachette remplace le « Le temps passe… » passif. | 21:57:20–21:58:40 | Souffle et sortie en timing. | Le meilleur moment du jeu devient encore meilleur (frustration 9). | petit | non |
| 10 | **« Continuer la nuit » après la vraie fin,** avec un tableau des fins manquantes (titre caché, indice énigmatique : « Quelqu'un vous offre un chèque… »). Une récompense pour les 5 souvenirs : une illustration bonus de l'équipe de 1989 aujourd'hui, ou un épilogue de Mme Delattre. | écran final | Chasser les fins, les souvenirs et les succès. | Rejouabilité (frustration 10). | petit (bouton et tableau) / moyen (illustration) | **oui**, 1 image pour la récompense des souvenirs, facultative |

Une idée de bonus, si vous voulez de l'émotion pure : **Mme Delattre** pourrait raconter Henri pendant que sa clé tourne pour rien dans la serrure (21:56:12), avec le choix de l'aider ou de lui dire doucement que la serrure a changé. Une scène de 20 s sans enjeu d'enquête, mais elle remplit le trou le plus vide avec ce que le jeu fait le mieux. Coût petit, pas de crédit.

---

## 7. Ce qu'il ne faut surtout pas toucher

- **L'ambiance et les illustrations.** L'écran-titre, la station sous la pluie, les fins illustrées (Odile qui pleure derrière sa vitre, le gilet jaune qui fume sur le parvis, le parloir) sont superbes.
- **L'écriture des petits détails.** L'extincteur « Révision — H. Delattre — 1997 », le gant « pour nos trente ans », la plaque de Lucien Marchal repassée au feutre par Bastien, le dessin « MAMAN CONDUIT LE TRAIN DU CIEL ». Les souvenirs sont la meilleure nouveauté : ils ne servent à rien pour l'enquête et c'est très bien comme ça.
- **La fausse piste de la clé.** « Mme Delattre. Elle a une clé du local. » → Odile rit, « pour la première fois de la soirée ». C'est drôle et tendre. Il en faut d'autres comme ça.
- **Les déjà-vu à partir de la boucle 3.** Ils sont parfaitement dosés.
- **La première minute** (la ronde, le gant, l'appel, l'hélice, le drone) est dense, vivante et bien signalée. Il faut seulement que les grosses révélations n'y tombent pas.
- **La cachette avec le souffle à retenir, et l'armoire** (l'ordre, puis le levier à maintenir, avec 6 s de pénalité en cas d'erreur). Ce sont les deux vrais gestes du jeu.
- **Le dilemme final** (« Fais ce qui est juste, Camille. Je ne te demanderai rien. »), sans bonne réponse, et ses deux épilogues.
- **Le bouton « Attendre »** avec ses moments nommés. Il est indispensable tant que les trous existent.
- **L'onglet « Personnes » du carnet**, très clair.

---

### Résumé en 10 lignes

1. C'est superbe (illustrations, pluie, détails d'écriture, souvenirs, déjà-vu dès la boucle 3), sans aucune erreur JS, et jouable sur iPhone.
2. Mais le jeu se désamorce : en soirée 1, à l'aveugle, reçu (21:54:05) puis café (21:55:34), et Odile avoue tout et reste ; 4 min 25 s de vide.
3. La piste reste bloquée sur « Observez… 22 h 00 » tant qu'on n'a pas vu la chute, même avec 26 indices.
4. Le badge est proposé avant sa fenêtre (21:54:55,7 contre 21:54:56) : échec garanti, et une soirée ruinée.
5. Le chemin optimal laisse 104 s puis 62 s d'attente, et le climax tient en 3 boutons dans une fiche, au distributeur.
6. Le micro est dans la pièce de l'armoire : l'utiliser revient à faire irruption, et le succès est quand même donné.
7. Bugs : toast d'appel affiché en retard, texte de l'interphone faux, bandeau d'annonce figé dans la cabine, « Le silence » sans illustration propre, bas du panneau final tronqué, étiquettes qui se chevauchent.
8. Notes : quantité 7, fun 5, tension 5, lisibilité 6, conséquences 6, rejouabilité 6, finition 7.
9. Priorités : un plan B de Vasseur quand Odile reste, des aveux en deux temps, une piste qui suit le savoir réel, une confrontation finale mise en scène.
10. À garder tel quel : la cachette, l'armoire, le dilemme final, la fausse piste de la clé, la première minute.
