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
