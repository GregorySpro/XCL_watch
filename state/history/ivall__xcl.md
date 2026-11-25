# Historique : ivall/xcl

## État du projet

Après ce changement, la gestion du heap `GLOBAL_SAMPLER` est fiable : les libérations sont correctement fusionnées, les requêtes de 8 slots ne sont plus bloquées par des fragments, et le tracing XCL montre les bons indices de samplers. Les tests de fuzz et la consommation mémoire liée à `XCL_TEXTURE_LEVEL_PAGES` restent inchangés et continuent d’attendre résolution. La prochaine étape probable reste l’implémentation de `DispatchMesh` et l’analyse des échecs de fuzz‑tests, maintenant que le sous‑système de samplers ne constitue plus un goulet d’étranglement.

## Journal (du plus récent au plus ancien)

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
