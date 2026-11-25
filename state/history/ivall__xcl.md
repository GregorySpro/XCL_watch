# Historique : ivall/xcl

## État du projet

Après ce correctif, le projet conserve ses deux backends DXIL, le fallback `pc`, le logging SSA, le pool DirectStorage SIMD et le `TilePool` contigu, avec tous les tests unitaires verts. Les nouvelles vérifications SCC et la gestion correcte de VCC améliorent la fiabilité du recompiler, notamment pour les shaders complexes, mais les performances du per‑lane form restent un point bloquant. Le nettoyage des PR inactives et l’optimisation du per‑lane form sont les prochaines étapes probables.

## Journal (du plus récent au plus ancien)

- **2026-11-25** `092d49a` D3D12.X: preserve rect-list GS when a cached graphics PSO blob is stale
- **2026-11-25** `94193f0` D3D12.X: the null device's struct-returning methods take the buffer first or second
- **2026-11-25** `982c641` Tests: add disasm integration coverage for --all flag, bounds checks and missing files
- **2026-11-25** `8ff5c31` Shaders: -O0 compiles leave out DXC's debug no-ops
- **2026-11-25** `2aa2d1a` Tests: cover permlane16, WQM, DS stride64, mulhi, i24, and u64 shifts
- **2026-11-25** `3f81044` Recompiler: translate v_div_fixup_f16 in both backends
- **2026-11-25** `c2dcef3` Recompiler: round direct-DXIL f16 converts and products to nearest-even
- **2026-11-25** `025c786` D3D12.X: the GPU wait watchdog starts after its wake pair is set
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
