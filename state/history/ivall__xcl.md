# Historique : ivall/xcl

## État du projet

Après l’intégration, le projet dispose désormais de tests robustes confirmant la cohérence du heap‑window entre les implémentations HLSL et DXIL, ce qui renforce la stabilité de la pile de recompilation shader. Les parties déjà fonctionnelles (partage de tiles VRAM, heap GLOBAL_SAMPLER, recompilation des shaders) restent intactes. Les blocages persistants sont les fuzz‑tests sur `XCL_TEXTURE_LEVEL_PAGES` et l’implémentation de `DispatchMesh`, ainsi que les cinq échecs déjà existants liés aux pipelines DXIL et aux masques bridgés. La prochaine étape probable sera de corriger ces échecs de fuzz‑tests et d’étendre les nouvelles vérifications de heap‑window à d’autres GPU (NVIDIA, AMD, Intel).

## Journal (du plus récent au plus ancien)

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
- **2026-11-25** `f4242ed` Shaders: read the per-stage root layout of console shader containers
- **2026-11-25** `3ceb550` D3D12.X: ExecuteIndirect skips failed shared compute stand-ins
- **2026-11-25** `38ac3d9` D3D12.X: quarantine unmapped parked sparse tiles behind GPU fences
- **2026-11-25** `22b6222` Direct DXIL: include pixel shader helper lanes in ballots and VOPC masks
- **2026-11-25** `e44af70` Recompiler: the FSSM header's wave32 flag decides a program's wave size
- **2026-11-25** `bd2ac50` Recompiler: v_frexp_mant_f32 / _f16 keep the input's sign (#580)
- **2026-11-25** `943043e` Recompiler: direct DXIL applies NEG_HI as ABS on v_dot2_f32_f16's accumulator (stacked on #1286)
