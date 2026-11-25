# Historique : Marvisak/VIctory

## État du projet

Après ce changement, le processus de démarrage de GTA VI réussit systématiquement (5 sur 5) même avec un pipeline cache important, et le debug layer D3D12 ne bloque plus le lancement. Le reste du pipeline (DXIL compact, rafraîchissement des textures, cache SSA) continue de fonctionner, ainsi que les tests unitaires du crate victory‑shader. Le rendu du monde est encore noir, HDR et depth‑bounds ne sont toujours pas implémentés, et le backend waterfall reste à intégrer. La prochaine étape probable reste l’ajout du backend waterfall et la prise en charge HDR/depth‑bounds.

## Journal (du plus récent au plus ancien)

- **2026-11-25** `2e319e5` Formats: add 7 invariant unit tests for xma/tables.rs
- **2026-11-25** `25b1bf3` Shaders: add SQ_IMG_SAMP LOD-bias and no-mip range tests for emit/sampler.rs
- **2026-11-25** `a7cb47c` GPU: no blending on integer render targets in host pipelines (fixes #806)
- **2026-11-25** `e278bc8` Shaders: hardware test for the s_cmpk_* scalar comparisons
- **2026-11-25** `b9c8a8b` victory-shader: build the per-instruction read/write lists once (ir::build -13%)
- **2026-11-25** `d1dfbbd` xmem: keep the space below 4 GiB for the title's 32-bit memory
  Le commit introduit un mécanisme de « low guards » qui réserve l’espace mémoire libre sous 4 GiB avant que le processus ne charge le driver, le cache et l’image, puis libère et réalloue ces gardes lorsqu’une allocation 32‑bits échoue. Cela vient s’ajouter aux précédentes améliorations de sérialisation WARP et de gestion des ressources shader, en débloquant le problème d’allocation qui faisait planter le démarrage du titre. Désormais le jeu peut démarrer même avec un gros cache pipeline ou le layer de débogage D3D12, mais le rendu du monde reste toujours noir et les fonctionnalités HDR/depth‑bounds restent absentes.
- **2026-11-25** `e9ea9a4` GPU: a full early pipeline list drops the oldest runs' keys
- **2026-11-25** `61bfc77` GPU: mirror placed unordered texture views
- **2026-11-25** `0665a0e` Shaders: scope wave64 ds_bpermute_b32 to 32-lane halves
- **2026-11-25** `bde8e7d` Shaders: share and serialize WARP host operations across threads
  Le nouveau commit introduit un dispositif WARP unique partagé au sein du processus et sérialise toutes les opérations host liées à WARP (création de device, root signatures, compilation de pipelines, draws et dispatches) derrière un mutex global. Cette mesure corrige les plantages d’accès violé (0xC0000005) observés sous Windows 11 lorsqu’on utilise plusieurs threads, poursuivant la série de correctifs récents (initialisation des UAV, mise en cache SSA, réduction des handles). Le reste du pipeline shader reste inchangé : le rendu du monde est toujours noir et les fonctionnalités HDR/depth‑bounds ne sont pas encore présentes.
- **2026-11-25** `792602e` Shaders: reuse SSA sums when tracing memory addresses
  Le nouveau commit introduit un cache des sommes d’adresses SSA dans le traceur de ressources shader, évitant de recomposer les mêmes additions à chaque utilisation. Cette optimisation poursuit la série d’améliorations du suivi des ressources (réduction du nombre de handles, initialisation des UAV, etc.) en passant d’un coût quadratique à linéaire pour les chaînes d’adresses. Elle accélère fortement le `resource::track` (jusqu’à 22× sur de gros scénarios) et ajoute des tests vérifiant la bonne prise en compte des adresses dynamiques et du titre‑memory. Le rendu du monde reste toujours noir ; aucune nouvelle fonctionnalité de rendu n’est débloquée.
- **2026-11-25** `30a5202` XAsync: run a failed call's cleanup without the completion lock (#114)
  Le commit introduit un drapeau atomique `claimed` dans la structure `Call` et modifie la logique de complétion pour que le premier thread qui termine libère le verrou avant d’exécuter le nettoyage du provider. Cette correction élimine le deadlock qui pouvait survenir lorsqu’un nettoyage attendait un thread déjà en cours de complétion. Elle s’inscrit comme une résolution de blocage dans le système d’async, indépendante des optimisations shader récentes, et rend désormais les appels XAsync fiables même en cas d’échec.
- **2026-11-25** `13f91ca` README: status of 2026-11-25, the prologue is reached at about 1 fps
- **2026-11-25** `67e8ae1` Shader tests: the compute harness zeroes its UAVs
  Le commit ajoute une fonction qui initialise à zéro tous les UAV utilisés comme cibles de copie avant le dispatch du compute shader, en copiant depuis un tampon d'upload rempli de zéros. Cette correction vient après les optimisations précédentes sur la réduction du nombre de handles et la gestion des pages, et résout un bug où la mémoire réutilisée par WARP conservait des bits résiduels, ce qui faisait échouer certains tests de shader. Désormais les buffers, textures et UAVs sont garantis à zéro, ce qui rend les tests de compute plus fiables, mais le rendu du monde reste toujours noir et les fonctionnalités HDR, depth‑bounds et waterfall sont toujours manquantes.
- **2026-11-25** `da82e44` Shader: normalized buffer codes convert to the float nearest their quotient
- **2026-11-25** `54ab406` Shaders: descriptors joined by nested control flow, again
- **2026-11-25** `315cea1` GPU: barriers start from the states resources are in: prelude order, the clamp's own buffer
- **2026-11-25** `4015a18` Shaders: quad reads in pixel programs reach the helpers at triangle edges
- **2026-11-25** `a1a2077` Shaders: vertex, geometry and mesh programs with UAVs say so in their flags
- **2026-11-25** `8681044` Shader tests: the differential harness binds the view-stride table
- **2026-11-25** `850b66f` Shaders: one handle per descriptor at a fixed address, and per join
  Le commit introduit une gestion unifiée des descripteurs : chaque descripteur à adresse fixe reçoit un seul handle, créé au bloc d’entrée ou au join correspondant, au lieu d’en créer un à chaque chargement. Cette optimisation réduit drastiquement le nombre de handles heap (de 2 608 à 65) et la taille du bitcode DXIL de 4 %. Elle s’inscrit dans la série d’améliorations du pipeline shader visant à diminuer les surcoûts liés aux copies et aux allocations, après les récents travaux sur le rafraîchissement des textures et la gestion des pages. Le rendu reste bloqué sur le noir du monde ; le changement ne touche pas les problèmes HDR, depth‑bounds ou le support waterfall.
- **2026-11-25** `286a00e` Shaders: the DXIL writer leaves out what no edge or reader reaches
- **2026-11-25** `8f2a6f1` GPU: a texture refresh reads title memory into its thread's own buffer
  Le commit fait lire la mémoire titre d’une texture rafraîchie dans un tampon persistant du thread de travail au lieu d’allouer un nouveau buffer à chaque rafraîchissement. Cette optimisation élimine les remplissages à zéro et les défauts de page qui coûtaient ~129 k défauts par image, réduisant le temps de rafraîchissement de 2,2 ms/MB à 1,8 ms/MB. Elle s’inscrit dans la suite d’améliorations du pipeline GPU (gestion des pages, copies, listes d’attente) et débloque une partie du coût de rafraîchissement des textures, mais le rendu du monde reste noir et les fonctionnalités HDR/depth‑bounds sont toujours absentes.
- **2026-11-25** `9cc37c5` GPU: lists wait on memory values; queues hold work until its waits hold
- **2026-11-25** `940d32f` GPU: a marked page's copy-back does not save the page for its own copy
  Le commit ajoute une fonction `unarm` qui retire une page de la liste des pages « armées » lorsqu’une copie‑back est en cours, et l’appelle avant de sauvegarder la page dans `shadow.rs`. Cela corrige le problème où une page marquée était re‑sauvegardée lors de sa propre copie, un bug identifié dans le suivi des pages GPU. Ce changement poursuit le chantier de stabilisation du pipeline de copie‑back, réduisant les risques de redondance et de corruption, mais n’affecte pas directement le rendu du monde qui reste noir.
- **2026-11-25** `cd94d35` GPU: shaders mark the pages they write through views; copy-backs compare only those
- **2026-11-25** `ccfb029` Tests: a machine with no GPU has no hardware adapter to check shaders on
- **2026-11-25** `42363ab` Tests: hardware-only shader checks skip on a machine without a GPU
- **2026-11-25** `4118f33` GPU: GetDeviceRemovedReason reports the host device's state instead of E_NOTIMPL
- **2026-11-25** `4436fca` Shaders: honor op_sel, omod and clamp on v_interp_p2_f16
- **2026-11-25** `5fa5de3` Shaders: a helper lane's v_readfirstlane reads its own value (#908)
- **2026-11-25** `fcdefcf` GPU: copy title writes under ranges a submission's shaders write
- **2026-11-25** `1bde03c` Shader tests: require a device readback in every waterfall test
- **2026-11-25** `3688adc` Shaders: GFX10 FLAT offsets ignore bit 11
- **2026-11-25** `13d1237` GPU: throttle failed swap-chain creation and resize
- **2026-11-25** `3c47fd4` GPU: fences answer for ID3D12Fence1 as documented
- **2026-11-25** `f73e8e1` Shaders: precompute MD5 round constants and avoid per-block Vec in md…
- **2026-11-25** `520fefc` Shaders: fold empty loop latches containing only dead phis
  Le commit corrige la détection des latches vides dans l’émission du bitcode en ignorant les phis morts, évitant ainsi que des boucles divergentes ne créent des blocs vides qui bloquent le driver AMD. Cette modification poursuit le chantier de stabilisation du pipeline shader en résolvant un bug d’émission qui pouvait laisser des latches non pliés. Le correctif ajoute un test unitaire confirmant que les latches contenant uniquement des phis morts sont correctement repliés, ce qui renforce la fiabilité du backend shader. Il ne touche pas directement le rendu du monde, mais élimine un risque de plantage lié aux shaders.
- **2026-11-25** `ef65f88` Handle unsupported display deferrals in the HLE test
- **2026-11-25** `19469bd` Shaders: hardware test for the integer v_min3/v_max3/v_med3
