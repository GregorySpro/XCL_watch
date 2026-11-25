# Historique : Marvisak/VIctory

## État du projet

Après ce correctif, le système de suivi des pages GPU évite désormais de sauvegarder une page déjà en cours de copie, ce qui améliore la fiabilité du pipeline shader (les 108 shaders traduits et les 249 bloqués restent inchangés). Le rendu du monde reste toujours noir, les fonctionnalités HDR et depth‑bounds sont toujours manquantes, et le support du waterfall doit encore être étendu aux requêtes de taille par lane. La prochaine étape probable reste l’intégration de ces informations dans le backend et la poursuite de l’extension du waterfall.

## Journal (du plus récent au plus ancien)

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
- **2026-11-24** `819f622` translate: progress counts finished shaders, not ones handed out
  Le commit ajuste le calcul du progrès affiché par le traducteur : il ne compte plus simplement les jobs distribués, mais soustrait les workers encore actifs pour ne retenir que les shaders réellement terminés. Cette modification poursuit le chantier d’amélioration de la visibilité du pipeline, corrigeant un affichage trompeur introduit précédemment. Elle ne change pas le comportement du moteur de shaders ni le support du waterfall, mais permet de suivre plus fidèlement l’avancement des traductions.
- **2026-11-24** `ffe310e` Shaders: the console's waterfall loop over a first-lane table offset
  Le commit ajoute la prise en charge du « waterfall » où chaque lane charge son propre descriptif à partir du décalage de la première lane, en marquant ces charges comme `per_lane` dans le générateur d’heap. Il enrichit la structure `Waterfall` avec une liste `loads` et intègre la détection de ces charges dans le suivi des shaders. Cette évolution poursuit les correctifs récents sur les boucles de vertex et les comparaisons, en débloquant partiellement la traduction des boucles de chargement non uniformes, mais le rendu du monde reste encore cassé.
- **2026-11-24** `ca48fbe` Merge pull request #566 from r4tedx/shader/per-lane-phi
- **2026-11-24** `74a9d33` Shader tests: the waterfall tests take the GPU one at a time, with the VALU tests
- **2026-11-24** `e09dbb6` Merge upstream main (8e79c80) into shader/per-lane-phi
- **2026-11-24** `8e79c80` CI: clippy and tests on pull requests and main
- **2026-11-24** `c7a5f49` Merge pull request #93 from TG0000/perf/bcpack-bucket-lookup
- **2026-11-24** `2005e35` Merge pull request #88 from AndyLatimer/fix/title-size-overflows
- **2026-11-24** `58fed37` Merge pull request #706 from Lyrnc/fix/cmp-lg-ordered
- **2026-11-24** `a51bdfa` Merge pull request #696 from catminou4/devin/formats-doc-micro-link
