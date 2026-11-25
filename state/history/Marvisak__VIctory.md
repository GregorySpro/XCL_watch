# Historique : Marvisak/VIctory

## État du projet

Après ce changement, le traducteur indique désormais le nombre exact de shaders traduits, en excluant ceux encore en cours d’exécution. Le reste du pipeline reste inchangé : le support du waterfall est actif, 108 shaders passent les boucles, 249 restent bloqués, le rendu du monde est toujours noir et les fonctionnalités HDR et depth‑bounds manquent. La prochaine étape probable reste l’extension du support du waterfall aux requêtes de taille par lane et l’intégration de ces informations dans le backend.

## Journal (du plus récent au plus ancien)

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
- **2026-11-24** `02f404e` Merge pull request #685 from catminou4/devin/constant-color-cfg-windows
- **2026-11-24** `5cd7421` Merge pull request #681 from fuzeofficlal/fix/vertex-buffer-null-unbind
- **2026-11-24** `8d23f07` Merge pull request #680 from EatCodeSleep2/fix/wine-staging-upload-128
- **2026-11-24** `22700b9` Merge pull request #675 from lameloball2813-ui/docs/rustdoc-private-links
- **2026-11-24** `6db02ea` Merge pull request #671 from fuzeofficlal/fix/copyresource-cpu-texture-source
- **2026-11-24** `16bd577` Merge pull request #666 from lameloball2813-ui/fix/load-probe-target-dir
- **2026-11-24** `b4b4110` Merge pull request #654 from SantannaCarlos/fix/uav-describe
- **2026-11-24** `cf0bb43` Merge pull request #637 from AlanQiang0418/codex/gfx10-packed-inline-constants
- **2026-11-24** `6f0a10a` Merge pull request #634 from SAitken91/shader/image-sample-o
- **2026-11-24** `9a92ccf` Merge pull request #612 from KoLolX3/fix/phi-liveness-transitive
- **2026-11-24** `204bdcc` Merge pull request #611 from AdvaitPalve11/fix/config-show-data-dir
- **2026-11-24** `a4d7b16` Merge pull request #565 from SAitken91/shader/conservative-depth-position
- **2026-11-24** `fd92558` Merge pull request #520 from Quat3rnion/victory-xasync-payload
- **2026-11-24** `dd54de2` Merge pull request #448 from Dufftwt/fix/min-max-signed-zeros
- **2026-11-24** `0ddbce8` Merge pull request #433 from r4tedx/tests/serialise-valu-gpu-tests
- **2026-11-24** `ce42e77` Merge pull request #432 from Proudnoob4/fix/uninitialized-sgprs
- **2026-11-24** `f0894c2` Merge pull request #429 from marquezinii/fix/xgamesave-documented-errors
