# Historique : ivall/xcl

## État du projet

Après l’intégration, le projet conserve ses performances compute et ses tests d’équivalence GPU qui continuent de réussir. Le cache de blobs entiers est maintenant exploité pour les shaders compute split‑eligible, ce qui fait chuter le temps de création de pipeline à environ 0,5 s et garantit la continuité en cas de blob manquant ou rejeté. La compaction automatique des shaders contenant des atomics est activée via la nouvelle logique `automatic_plan`, tout en restant contrôlable par `XCL_DIRECT_COMPACT`. Les problèmes ouverts restent les fuzz‑tests, DispatchMesh et la compatibilité du mode de compaction avec les drivers qui refusent le mode heap; la prochaine étape probable reste l’optimisation des stores/atomics compute et la poursuite des fuzz‑tests.

## Journal (du plus récent au plus ancien)

- **2026-11-26** `b49be18` Shaders: reuse offline compute PSOs and compact repeated atomic shaders
  Le nouveau PR réactive la réutilisation des blobs complets de pipelines compute en cache et ajoute la compaction DXIL pour les blocs atomiques répétés, avec un mode de sélection automatique. Cette évolution poursuit le chantier d’optimisation des pipelines compute amorcé par #2088, en complétant les précédentes améliorations de cache et de compaction. Elle permet désormais de charger un blob entier au runtime et, en cas d’échec, de retomber sur le découpage traditionnel, tout en réduisant fortement le temps de création de pipeline. Le reste des blocages (fuzz‑tests, DispatchMesh, drivers refusant le mode heap) demeure inchangé.
- **2026-11-25** `f8f9de6` Shaders: reuse offline compute PSOs and compact repeated atomic shaders
- **2026-11-25** `c836ecd` Shaders: reuse offline compute PSOs and compact repeated atomic shaders
  Le nouveau commit ajoute la prise en charge de la compaction automatique des blocs compute « split‑wave » contenant des atomics de type BUFFER_ATOMIC_ADD. Il introduit une fonction `atomic_candidate` et un plan `automatic_plan` qui sélectionnent la forme compacte lorsqu’un shader est suffisamment grand ou comporte des atomics retournants, remplaçant la logique précédente basée uniquement sur la taille. Cette évolution poursuit le chantier d’optimisation des pipelines compute amorcé par #2088 et les précédents PR #174 et #2371, en élargissant le critère de compaction aux shaders atomiques. Désormais les shaders compute avec atomics peuvent être exécutés via le cache de pipeline complet, réduisant le temps de création de pipeline, tandis que les cas sans atomics restent inchangés.
- **2026-11-25** `ec80c07` Shaders: reuse offline compute PSOs and compact repeated atomic shaders
  Le nouveau commit introduit la réutilisation des pipelines compute complets en cache avant de les découper en morceaux, ainsi qu’une version compacte du DXIL pour les blocs atomiques répétés. Cette évolution poursuit le chantier d’optimisation des pipelines compute amorcé par #2088 et vient compléter le mécanisme de lien des accesseurs mémoire. Désormais les shaders compute qui peuvent être découpés profitent d’un cache de blobs entiers, réduisant fortement le temps de création de pipeline, tandis que les cas où le blob est absent ou rejeté continuent à être découpés comme avant.
- **2026-11-25** `08db802` DXC: compute programs are linked with their memory accessors (window switches no longer dominate DXC time)
  Le nouveau PR #174 fait lier les programmes compute avec leurs accesseurs de mémoire, ce qui élimine les commutateurs de fenêtres comme facteur dominant du temps de compilation DXC, gagnant environ 14 % de rapidité. Cette amélioration poursuit le chantier d’optimisation des accès mémoire amorcé par les précédents commits de flattening et de tests d’équivalence GPU, tout en restant conditionnée à l’option `XCL_HEAP_WINDOWS=1`. Désormais les programmes en forme « heap » sont compilés sans accesseurs de commutation, tandis que le mode « switch » reste disponible lorsque le driver refuse le mode heap. Le reste du projet (shaders compute rapides, tests GPU heap‑window qui passent) demeure inchangé, mais les builds DXC restent dépendants d’une version ≥ 1.8 pour le lien.
- **2026-11-25** `e1d84b7` README: the switches for a bug report's diagnostics
  Le nouveau commit ajoute une section « Diagnostics for a bug report » au README, listant sept variables d’environnement que l’on peut activer pour obtenir des traces détaillées (durée d’exécution, activité périodique, dump de threads, traces de fautes d’accès, traces de mémoire, traces d’imports et trace D3D12). Cette documentation vient compléter le chantier de visibilité des réglages utilisateur, sans toucher au code. Désormais les développeurs et testeurs peuvent activer ces diagnostics pour mieux comprendre les blocages, mais cela ne résout pas les problèmes techniques encore ouverts (fuzz‑tests, DispatchMesh, échecs DXIL).
- **2026-11-25** `6d12f58` Recompiler: flatten direct DXIL root-UAV loads for compute
  Le commit a remplacé les multiples branches `switch` utilisées pour chaque chargement direct DXIL root‑UAV par une séquence linéaire utilisant `rawBufferLoad` et `select` sur les shaders compute. Cette optimisation poursuit le chantier amorcé par le PR #2088 : réduire le nombre de blocs basiques générés en mode direct DXIL, ce qui diminue drastiquement les temps de compilation à froid et la consommation mémoire du driver NVIDIA. Le reste du pipeline (stores, atomics, chemins HLSL et graphics) reste inchangé, et les performances des shaders pixel restent identiques.
- **2026-11-25** `e0d93c8` D3D12.X: a dispatch waits for its pipeline at the submit, not in the bind
- **2026-11-25** `6d051da` Recompiler: load direct DXIL geometry ES inputs from the heap window
- **2026-11-25** `fda0b1f` Tests: verify heap-window GPU equivalence and graphics validation
  Le nouveau commit ajoute une batterie de tests de validation de l’équivalence GPU pour le mode `XCL_HEAP_WINDOWS=1`, couvrant les chargements, stockages, atomiques 32 bits et 64 bits ainsi que la validation des shaders graphiques sur plusieurs étapes. Ces tests s’inscrivent dans la continuité des récents travaux de vérification des tables de layout et des comparaisons avec le driver natif, en ciblant spécifiquement la correction du comportement des fenêtres de heap. Ils montrent que, sur le RTX 5090, les chemins HLSL et DXIL produisent des résultats bit‑for‑bit identiques, mais ne résolvent pas les échecs déjà présents dans les fuzz‑tests de création de pipeline et de masques bridgés.
- **2026-11-25** `889ef3e` Tests: compare extra-page tiles when an unaligned va is placed again (#718)
- **2026-11-25** `72f848c` Tests: host comparisons take the driver's own answer under Wine
- **2026-11-25** `3f60df5` Tests: host comparisons take the driver's own answer under Wine
- **2026-11-25** `06055a7` Tests: verify D3D12.X guard, split, and tracer thunks for game callers
- **2026-11-25** `8f87d72` Tests: verify D3D12.X console queue descriptor translation
- **2026-11-25** `2715714` Tests: compare CreateReservedResource against the host D3D12 driver
- **2026-11-25** `44eb541` Tests: pin the pipeline stream subobject table to the PC headers
- **2026-11-25** `d48b065` Tests: assert the refused depth-buffer UAV state on Windows only
- **2026-11-25** `27fa5f3` Tests: honor the selected adapter in the indirect argument test
- **2026-11-25** `38c3862` Tests: wait for the late release before freeing the coherent buffer test's heap (#1487)
- **2026-11-25** `23865ea` Tests: a failed copy scratch check no longer aborts the lib test binary
- **2026-11-25** `8a3f912` Tests: pin the generated D3D12 layout table invariants
- **2026-11-25** `72b59b5` Tests: console descriptor heap slot/copy/unregister contract
- **2026-11-25** `2664021` Tests: compare GetResourceAllocationInfo and GetCustomHeapProperties against host D3D12 driver
- **2026-11-25** `8b38c1d` Tests: verify CreateHeap console fallback and IID retry against host driver
- **2026-11-25** `661373a` Tests: compare CheckFeatureSupport hook against host D3D12 driver
  Le nouveau commit introduit un test d’intégration autonome qui compare le hook `CheckFeatureSupport` d’XCL avec l’implémentation native du driver D3D12 de la machine hôte. Il s’inscrit dans la série de tests récents visant à valider le comportement des API graphiques sans dépendre de jeux ou de fichiers console, poursuivant ainsi le chantier de fiabilisation des abstractions D3D12. Désormais on peut vérifier que les requêtes de support de fonctionnalités retournent exactement les mêmes résultats que le driver hôte, y compris la gestion des structures surdimensionnées et des formats spécifiques console, ce qui était auparavant non testé. Le reste du projet reste inchangé : les améliorations du heap GLOBAL_SAMPLER et les autres tests continuent de fonctionner, mais les fuzz‑tests et la prise en charge de DispatchMesh restent non résolus.
- **2026-11-25** `6521351` Tests: cover Wait64 recording and satisfied batch submission
- **2026-11-25** `04f4091` Tests: skip the raw heap-window view test on a device without SM 6.6
- **2026-11-25** `9ba6804` Tests: skip the WARP frame-dump test where WARP is unavailable
- **2026-11-25** `7958d6b` Tests: a panicking title-texture test no longer poisons the others
- **2026-11-25** `3111b5f` Tests: check cubemap tile reuse independently of idle page reclamation
  Le nouveau commit ajoute un helper de test qui capture l’état des tiles VRAM d’une ressource et renforce les assertions du test cubemap pour vérifier que le second cube réutilise exactement les mêmes tiles que le premier, même en présence de libération d’autres textures. Cette modification s’inscrit dans la série de correctifs récents visant à stabiliser la gestion du heap et du paging (quarantine des tiles, ExecuteIndirect, lecture des layouts de shaders, optimisations du recompiler). Elle montre que le sous‑système de réutilisation de tiles fonctionne désormais de façon déterministe, mais ne touche pas les problèmes restants de fuzz‑tests ou de consommation liée à XCL_TEXTURE_LEVEL_PAGES.
- **2026-11-25** `2898511` Recompiler: eliminate local array and branches in pixel-shader PRELUDE::rla()
- **2026-11-25** `f9f6d49` Recompiler: key rerouted DXIL by compact nesting policy
- **2026-11-25** `e3f042c` Shaders: prebuild split compute dispatch-chain pieces under --pipelines
- **2026-11-25** `c56e744` Tests: check divergent block barriers in a_bridged_mask_write
- **2026-11-25** `3f27770` Recompiler: coalesce GS header and vertex attribute stores in direct DXIL
- **2026-11-25** `7292dca` Recompiler: specialize constant-mask v_cmp_class_f32 in direct DXIL
- **2026-11-25** `c7e1799` Recompiler: lower V_MIN_U32, V_MAX_U32, V_MIN_I32, and V_MAX_I32 in p…
- **2026-11-25** `2e40b78` Recompiler: fold shift-masks, nested masks, double-xor, and all-ones or in direct DXIL opt
- **2026-11-25** `ae6f284` Recompiler: translate v_qsad_pk_u16_u8 and v_mqsad_{pk_u16,u32}_u8 in both backends (takes over #909)
