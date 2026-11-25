# Historique : ivall/xcl

## État du projet

Après ce correctif, le projet conserve ses deux backends DXIL, le fallback `pc`, le logging SSA, le pool DirectStorage SIMD et le `TilePool` contigu, avec tous les tests unitaires verts. Les nouvelles vérifications SCC et la gestion correcte de VCC améliorent la fiabilité du recompiler, notamment pour les shaders complexes, mais les performances du per‑lane form restent un point bloquant. Le nettoyage des PR inactives et l’optimisation du per‑lane form sont les prochaines étapes probables.

## Journal (du plus récent au plus ancien)

- **2026-11-25** `c00f3fc` Direct DXIL: honour D16 packing in image_get_resinfo
- **2026-11-25** `fb32c87` UMA: support 8/16 heap windows (5/13 coherent) with 3-sparse (12 GiB) cap
- **2026-11-25** `6c9f947` Recompiler: saturate UINT/SINT and use RTZ in direct DXIL fmt_encode
- **2026-11-25** `3da2abb` Recompiler: direct DXIL's signature dependencies are complete across loops
- **2026-11-25** `edf8c16` Recompiler: honor FP32 denormal mode in FREXP exponent
- **2026-11-25** `86719ee` Direct DXIL: write every dword of an unaligned multi-dword format store
- **2026-11-25** `393cbdd` Recompiler: lower S_FF0_I32 and S_FLBIT_I32 on mask registers in the per-lane form
- **2026-11-25** `8f5fca5` D3D12.X: detile 3D textures with packed mip tails
- **2026-11-25** `2bdf047` D3D12.X: reduce retained texture upload staging
- **2026-11-25** `99e4743` Recompiler: select vertices for BVH triangle node types 2 and 3
- **2026-11-25** `6f45624` Recompiler: fix per-lane whole-wave predicate and SRC_SCC checks
  Le commit corrige la logique de prédicats whole‑wave en mode per‑lane, en introduisant le suivi du type de réduction SCC (Uniform, Any, All) et en arrêtant l’AND de VCC avec EXEC lorsqu’on n’utilise pas de branches wave‑wide. Cela poursuit les efforts récents sur le recompiler (classification wave64, gestion des masques) et comble trois failles de soundness qui bloquaient la précision des shaders volumineux. Désormais les analyses de masques tiennent compte des lanes inactives et les tests unitaires valident ces cas, mais les optimisations de per‑lane form restent encore à améliorer.
- **2026-11-25** `bf9eebd` Recompiler: keep entered far-jump sequences indirect
- **2026-11-25** `7b9eabb` Recompiler: v_rcp_f64 and v_rsq_f64 keep NaN, infinities and denormals
- **2026-11-25** `63547b7` Shaders: xcl disasm prints SMEM offsets, glc and dlc as LLVM does (#664)
- **2026-11-25** `da6174d` Memory: bound sparse UMA first-fit searches by address
- **2026-11-25** `0e45b97` Tests: label bench_split_wave64 by the form x64_spin really picks
- **2026-11-25** `b282d6b` Tests: the sparse VRAM test binds the heaps its root signature indexes
- **2026-11-25** `0be6ce8` DirectStorage: use SIMD for zlib checksums
  Le nouveau commit active l’option SIMD du crate `miniz_oxide` pour le calcul du checksum Adler‑32 utilisé par DirectStorage, en ajoutant la dépendance `simd-adler32`. Cela poursuit la série d’optimisations récentes (ex. gestion des masques, réduction des branches) en ciblant un goulet d’I/O constaté lors du chargement de titres retail. Le changement apporte un gain de ~5 % sur les décompressions de blocs de 64 KiB, tout en conservant la même exactitude. Il ne résout pas les problèmes persistants de shaders trop gros ou de per‑lane form, mais réduit un autre point de latence.
- **2026-11-25** `b8428c5` XAudio2: precompute XMA inverse quantizer gains
- **2026-11-25** `279d3ea` Recompiler: fold split-wave64 reduction tail v_readlane exchanges
- **2026-11-25** `152de4e` acphal: a reconnect drops messages queued for the old channel
- **2026-11-25** `137b542` GameInput: a DualShock 4 or DualSense is the gamepad without XInput
- **2026-11-25** `5796cc4` Recompiler: classify wave64 VS / PS by whether a 32-lane cut changes them (#336)
- **2026-11-25** `400e638` Pixel shaders: masks hold the helper lanes (#105 item 1)
- **2026-11-25** `de36462` Shaders: size-based admission for big compute driver compiles
- **2026-11-25** `2e1c956` dxil: lower 64-bit icmp via 32-bit dwords and guard MIMG heap indices
- **2026-11-25** `7994cd1` Direct DXIL: TG_SIZE counts a partial last wave
- **2026-11-25** `6567c5f` Recompiler: take native compute mask branches across the whole wave
- **2026-11-25** `8c4a46a` D3D12.X: default DepthEnable to false in stream_pipeline when DSVFormat is UNKNOWN
- **2026-11-25** `2c95f01` D3D12.X: declare triangle topology for translated mesh PSOs
- **2026-11-25** `80f4819` D3D12.X: cache direct-DXIL mesh shaders, coalesce shader builds, make MS/GS lazy, and cache two-pass/graphics PSO blobs
  Le nouveau commit introduit un système de mise en cache et de coalescence pour les shaders DXIL directs, notamment les mesh shaders, ainsi que la mise en cache paresseuse des traductions MS/GS et des blobs PSO graphiques. Cette amélioration poursuit le chantier d’optimisation du pipeline DXIL amorcé par les correctifs précédents (gestion du CSE, réordonnancement des textureLoad, etc.) en ciblant la vitesse de compilation et la réutilisation des PSO. Désormais les compilations identiques sont partagées entre threads et les blobs PSO sont stockés sur disque, ce qui réduit les temps de pré‑chargement ; les limitations de taille de shaders ou de per‑lane form restent inchangées.
- **2026-11-24** `8e87fc7` Direct DXIL: truncate s instead of returning native on special values in round_exact
- **2026-11-24** `332122d` Direct DXIL: avoid 2 - off underflow in s_setreg_b32 targeting HW_REG_MODE bit 3
- **2026-11-24** `fc14fab` Recompiler: allow inline-constant and NULL mask operands in the per-lane form
- **2026-11-24** `36ac23d` Recompiler: invalidate block-local DXIL CSE wave/quad cache across dx.op.discard
  Le commit corrige le cache d’élimination de sous‑expression (CSE) locale du recompileur DXIL en le vidant lorsqu’un appel `dx.op.discard` est rencontré. Cette modification poursuit la série d’ajustements du backend DXIL (émission correcte des textureLoad, gestion des pipelines compute) en résolvant un bug où des appels `dx.op.wave*` ou `dx.op.quad*` après un discard réutilisaient des résultats invalides. Désormais les shaders pixel qui utilisent `dx.op.discard` conservent la bonne sémantique : les appels wave/quad après le discard ne sont plus fusionnés, ce qui évite des comportements erronés. Le reste du projet reste inchangé, aucune régression n’est observée.
- **2026-11-24** `62265d3` Per-lane: track the SCC of mixed mask and number operations
- **2026-11-24** `95ae589` Recompiler: a float v_cmpx through #1426's lowering keeps lanes outside EXEC out
- **2026-11-24** `f5136a6` Fast-boot: opt-in Bink intro skip, frame interval override, device type and TTS mute env vars
- **2026-11-24** `a713101` Memory: skip VirtualQuery on committed coherent UMA windows in cpu_writes and kernelx
  Le nouveau commit ajoute un fast‑path qui évite les appels coûteux à VirtualQuery sur les fenêtres UMA cohérentes déjà engagées, en les détectant via `uma.in_committed_window`. Cette optimisation s’inscrit dans la suite des améliorations de la gestion mémoire et du pipeline CPU‑GPU, suivant les récents travaux sur les backends DXIL et la réduction des blocages liés aux mises à jour de tuiles. Désormais les écritures CPU (`write_now`, `write_u64_now`, `writable`) et les requêtes de trace de vertex‑buffer utilisent ce raccourci, ce qui réduit les latences de 75‑80 ms observées auparavant. Le reste des fonctionnalités reste inchangé ; les performances globales du per‑lane form et les limites de shaders volumineux ne sont pas affectées.
- **2026-11-24** `20cf130` dxil: emit MSAA textureLoad before getDimensions to avoid amdxc64 crash
  Le commit réordonne les appels DXIL en émettant d’abord `dx.op.textureLoad` puis `dx.op.getDimensions` pour les textures MSAA dynamiquement indexées, éliminant le plantage du compilateur AMD (`amdxc64.dll`) lors de la création de pipelines compute. Cette correction suit le précédent correctif #425b869 qui visait déjà les crashes liés aux textures MSAA, et vient clôturer le blocage qui empêchait l’exécution de shaders compute contenant des `Texture2DMS`/`Texture2DMSArray`. Désormais les shaders MSAA passent la compilation et l’exécution sur les GPU AMD, mais les autres problèmes (taille des shaders, lenteur CI, optimisation SSA) restent en suspens.
