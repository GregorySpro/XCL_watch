# Historique : ivall/xcl

## État du projet

Après ce changement, la gestion du heap `GLOBAL_SAMPLER` est fiable : les libérations sont correctement fusionnées, les requêtes de 8 slots ne sont plus bloquées par des fragments, et le tracing XCL montre les bons indices de samplers. Les tests de fuzz et la consommation mémoire liée à `XCL_TEXTURE_LEVEL_PAGES` restent inchangés et continuent d’attendre résolution. La prochaine étape probable reste l’implémentation de `DispatchMesh` et l’analyse des échecs de fuzz‑tests, maintenant que le sous‑système de samplers ne constitue plus un goulet d’étranglement.

## Journal (du plus récent au plus ancien)

- **2026-11-25** `e44af70` Recompiler: the FSSM header's wave32 flag decides a program's wave size
- **2026-11-25** `bd2ac50` Recompiler: v_frexp_mant_f32 / _f16 keep the input's sign (#580)
- **2026-11-25** `943043e` Recompiler: direct DXIL applies NEG_HI as ABS on v_dot2_f32_f16's accumulator (stacked on #1286)
- **2026-11-25** `7ff2b2e` Shaders: cache translator refusals and DXC compile failures by capability
- **2026-11-25** `c9ee916` README: the switches for how a title runs
- **2026-11-25** `0437ecd` CLI: reject unknown options and invalid values in xcl rpf and xcl shaders (#2266)
- **2026-11-25** `569f0fc` DXIL: lower unclamped v_dot8_u32_u4 using Dot4AddU8Packed
- **2026-11-25** `31608c9` GPU: use CreateDXGIFactory2 and clean fallback/skip in large texture upload test
- **2026-11-25** `728e254` GPU: fail closed on unresolved control flow and errored FSSMs in effects
- **2026-11-25** `be67bfc` Recompiler: an S# word written with s_brev_b32 is a constant too
- **2026-11-25** `9ed0c13` Shaders: reverse the halves of an i64 Bfrev with shifts and masks on AMD
- **2026-11-25** `59cdc71` Direct DXIL: guard dp_mask in s_setreg_b32 when writing MODE[1:0]
- **2026-11-25** `397ea96` D3D12.X: ensure view and sampler descriptor heaps before console-list dispatches
- **2026-11-25** `9bcb867` D3D12.X: coalesce released shared sampler ranges and fix view/census checks in console_heap
  Le commit fusionne la PR #2261 qui corrige trois bugs du tas partagé de samplers globaux (`GLOBAL_SAMPLER`). Il ajoute la coalescence des plages libérées et ignore les libérations à zéro, exclut les tas de samplers des vérifications `in_global` et utilise les indices réels des pages de samplers dans le recensement. Ces correctifs poursuivent le chantier amorcé par #1991, qui visait à stabiliser la gestion des descripteurs de samplers et à éviter les épuisements prématurés. Désormais le runtime ne tombera plus dans le fallback du slot 0 lorsqu’une petite page de samplers est libérée, et le tracing des samplers partagés sera plus précis, même si les autres parties du projet (ex. `DispatchMesh`) restent inchangées.
- **2026-11-25** `d508006` D3D12.X: align promoted UAV clear values, key UAV support by adapter LUID, and use device-aware footprints fallback
- **2026-11-25** `b328113` Direct DXIL: sign-extend i1 to i64 through i32
- **2026-11-25** `ed95117` D3D12.X: fence reclaimed VRAM heaps across all live queues and isolate arenas by device
- **2026-11-25** `db4ad09` Tests: avoid poisoning SYNC_GRAPH with an unmapped fake fence in queue_tests (#1978)
- **2026-11-25** `f54019e` xcl run: a panic on any thread is written to xcl.log
- **2026-11-25** `88d7a98` Shaders: list offline precompile options in CLI help
- **2026-11-25** `e89f5ca` dxil: support 64-bit typed image_atomic_* via SM 6.6 heap atomics
  Le commit ajoute la prise en charge des instructions atomiques d’image 64 bits (`image_atomic_*`) via les heap atomics SM 6.6, en introduisant du code de bas niveau pour détecter ces cas, générer les opérations DXIL appropriées et gérer les variantes (add, sub, cmpswap, fcmpswap, etc.). Cette évolution poursuit le chantier de complétude du recompiler, qui avait déjà ajouté le support DPP et les masques, en débloquant maintenant les shaders qui utilisent des atomiques 64 bits. Désormais les tests unitaires valident ces nouvelles variantes, mais les parties dépendantes (ex. DispatchMesh) restent inchangées.
- **2026-11-25** `73e3aba` Shaders: xcl disasm prints DPP controls, masks and source modifiers
  Le nouveau commit enrichit le désassembleur `xcl disasm` en affichant les contrôles DPP, leurs masques et les modificateurs de source, qui étaient auparavant omis. Cette amélioration poursuit le chantier de visibilité du shader en complétant le support déjà ajouté pour les variantes SDWA. Désormais les lignes DPP sont imprimées avec toutes les informations de contrôle (quad_perm, row_shl, row_mask, bank_mask, etc.) et les signes/abs des sources, ce qui facilite la comparaison avec LLVM et le débogage. Le reste du projet reste inchangé : les DLL multi‑`*` sont toujours gérées, le garde `IN_CREATOR` empêche toujours les crashes DRED, et les slots `DispatchMesh` restent absents.
- **2026-11-25** `68a284a` D3D12.X: a runtime-made resource inside a device creation call is not adopted
  Le commit ajoute un garde thread‑local `IN_CREATOR` qui détecte quand l’appel `creator!` provient d’une création de ressource interne du runtime (le tampon d’auto‑breadcrumb DRED généré par D3D12Core lors de `CreateCommandList`). Dans ce cas la création est déléguée directement au runtime, évitant l’adoption erronée du resource et le crash nul‑pointer. Cette correction poursuit le chantier de stabilité de la couche avec le debug layer DRED, résolvant le blocage #2298. Désormais les tests sous le debug layer avec `XCL_DRED=1` passent, mais les slots `DispatchMesh` et les fuzz‑tests restent non résolus.
- **2026-11-25** `50a9fde` Recompiler: a bridged mask write in the split form meets no barrier
- **2026-11-25** `af066dd` Recompiler: direct compact form stores a branch's target in the entry
- **2026-11-25** `55d64c2` D3D12.X: console UAV clears (#53 / #54) get the slots' PC descriptors
- **2026-11-25** `65fc114` D3D12.X: composite multi-plane PresentX overlays
- **2026-11-25** `9f02f9d` kernelx: XMemMapPhysicalPagesScatter with no page array unmaps
- **2026-11-25** `30ba7a2` D3D12.X: XCL_TEXTURE_LEVEL_PAGES=1 gives a sampled texture's levels their pages with their texels
  Le commit introduit la variable d’environnement `XCL_TEXTURE_LEVEL_PAGES=1` qui différencie l’allocation des pages vidéo des niveaux d’une texture échantillonnée : les pages ne sont réservées que lorsqu’un niveau reçoit réellement des texels, au lieu d’être allouées toutes d’un coup lors du placement initial. Cette évolution poursuit le chantier d’optimisation de la gestion de la mémoire vidéo amorcé par les correctifs de configuration et de scratch, en ciblant le problème de sur‑allocation des textures streamées. Désormais les titres qui streament leurs textures utilisent beaucoup moins de pages VRAM inutilisées, mais la prise en charge reste désactivée par défaut et les effets sur les performances ou la stabilité restent à confirmer.
- **2026-11-25** `ce6a880` DirectStorage: read a physical-page list again when its request runs
- **2026-11-25** `3f4495a` Recompiler: emit globallycoherent on compute memory windows only when needed
- **2026-11-25** `e9f129d` XCL_NETWORK=0: GetAddrInfoExW answers local names like getaddrinfo does
- **2026-11-25** `764d9ed` Config: device_type in xcl.toml names the Series S in every spelling
- **2026-11-25** `4114da8` Config: an alias pattern with several '*' matches its DLLs
  Le nouveau commit corrige la fonction `glob_match` pour qu’elle gère correctement plusieurs jokers `*` dans les motifs d’alias du fichier `xcl.toml`. Cette amélioration poursuit le chantier de fiabilité de la configuration, en débloquant la résolution des DLL d’API‑set comme `api‑ms‑win-*-l1-1-*.dll` qui était auparavant ignorée. Désormais les alias multi‑étoiles sont reconnus, ce qui élargit la compatibilité des jeux dépendant de ces bibliothèques. Le reste du projet (shaders, DRED, splits) reste inchangé.
- **2026-11-25** `0829441` D3D12.X: the scratch of split dispatches is bounded over all recordings
- **2026-11-25** `e9ddf17` D3D12.X: a split dispatch runs in slabs that share a bounded scratch
- **2026-11-25** `625a0f3` D3D12.X: stand-ins of pipelines not built yet share the driver's pipelines
- **2026-11-25** `12f25f1` Recompiler: whole-wave reductions of wave32 VS / PS count every live lane
- **2026-11-25** `7906df7` Recompiler: pixel shader loops end on primitive edges (helper lanes)
  Le nouveau commit modifie le recompileur pour que `v_readfirstlane` lise la première lane réellement active (live) dans les pixel shaders, et que les tests de masque de lanes ne comptent que les lanes vivantes. Cela poursuit le chantier amorcé par les précédents correctifs sur les boucles de pixel shaders et les lanes helpers, résolvant le plantage GPU sur les cartes AMD RDNA2 lors du passage volumétrique. Désormais les boucles qui dépendaient de masques d’exécution se terminent correctement, mais le traitement complet de `DispatchMesh` reste toujours absent.
- **2026-11-25** `8fcc111` Recompiler: pixel shader loops end on primitive edges (helper lanes)
  Le nouveau commit modifie le recompileur pour que les tests de masque de lanes dans les pixel shaders ne comptent que les lanes actives. En pratique, les boucles qui s’appuient sur un masque issu de EXEC se terminent correctement, même sur les drivers AMD RDNA2 qui bloquaient les lanes helpers. Cette amélioration poursuit la série de correctifs sur les boucles de pixel shader (v_readfirstlane et masques) et débloque enfin le cas de la passe de lumière volumétrique qui faisait planter le GPU. Il reste toutefois les slots DispatchMesh non implémentés et les fuzz‑tests qui continuent d’échouer.
