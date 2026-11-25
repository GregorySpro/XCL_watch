# Historique : Marvisak/VIctory

## État du projet

Après ce changement, le pipeline shader génère un DXIL plus compact et crée beaucoup moins de handles, ce qui devrait alléger la charge du compilateur et du runtime. Le rafraîchissement des textures continue de fonctionner avec les gains précédents, et les correctifs de gestion de pages restent en place. Le rendu du monde est toujours noir, HDR et depth‑bounds manquent, et le support waterfall doit encore être intégré. La prochaine étape probable reste l’ajout des informations waterfall dans le backend et la poursuite de l’extension du support waterfall.

## Journal (du plus récent au plus ancien)

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
- **2026-11-25** `fdce5f5` Shaders: negative-zero ordering test for the f16 min/max forms
- **2026-11-25** `4bf5100` Check the required package archive before title startup
- **2026-11-25** `26e03c3` XGameSave: a stray or unreadable version folder never costs the save (#870)
- **2026-11-25** `d4ef9eb` HLE: show title windows through a stand-in that always reads its messages
- **2026-11-25** `88de5fc` run: stop with a clear message when the title's own DLLs are missing
- **2026-11-24** `70564ae` README: status of 2026-11-24
- **2026-11-24** `7000573` Diagnostics: title memory peeks, slot states, small buffers and 1D/3D read-back in listed frames
- **2026-11-24** `9c58a1c` Pipeline pre-build from the shader pack (translate --cache --pipelines); mesh pipelines load from th
- **2026-11-24** `bacb06a` GPU: a heap slot holds its view's own copy
- **2026-11-24** `6d021fe` GPU: world frame cost
- **2026-11-24** `86a5ed6` GPU: views off their buffer's elements get their own copy; heap records copied as bytes are mirrored
- **2026-11-24** `224e786` GPU tests: the frame pacing test tolerates a busy host
- **2026-11-24** `13bbd6f` GPU: one host texture per texture memory, and writable twins for depth textures
- **2026-11-24** `e0034c2` Shaders: register-relative moves reach only what their index can
