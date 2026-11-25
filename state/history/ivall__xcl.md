# Historique : ivall/xcl

## État du projet

Après ce changement, le projet conserve ses deux backends DXIL, le fallback `pc`, le logging SSA, le pool DirectStorage parallélisé et le `TilePool` contigu, avec tous les tests unitaires verts. Les nouvelles caches de shaders et de blobs PSO accélèrent le throughput de compilation et diminuent les blocages lors du preload, mais les problèmes de shaders trop gros et d’optimisation du per‑lane form persistent. La prochaine étape probable reste l’amélioration des performances du per‑lane form tout en continuant le nettoyage des PR inactives.

## Journal (du plus récent au plus ancien)

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
- **2026-11-24** `425b869` dxil: emit MSAA textureLoad before getDimensions to avoid amdxc64 crash
  Le commit modifie le backend DXIL pour émettre les appels `dx.op.textureLoad` avant `dx.op.getDimensions` dans les blocs basiques contenant des textures MSAA dynamiquement indexées, évitant ainsi le plantage du compilateur AMD (`amdxc64.dll`). Cette correction s’inscrit dans la suite des améliorations du support MSAA introduites récemment (geometry mode, MSAA loads) et résout un blocage qui empêchait la création de pipelines compute sur certaines cartes AMD. Désormais les shaders MSAA dynamiques peuvent être compilés sans crash, mais les autres limitations (shaders trop gros, optimisation SSA) restent inchangées.
- **2026-11-24** `689f2db` Direct DXIL: geometry mode, MSAA loads and directed f32 rounding
- **2026-11-24** `e9ac9f0` Direct DXIL: geometry mode, MSAA loads and directed f32 rounding
  Le nouveau commit ajoute un workflow GitHub Actions qui marque les pull‑requests inactives depuis trois jours comme « stale » puis les ferme après une semaine, sauf si elles portent les labels `goal:world-load` ou `keep-open`. Cette automatisation poursuit le chantier CI initié précédemment (voir le commit 1d027e5) et ne touche pas le code du recompiler ou les backends. Elle ne débloque aucune fonctionnalité du moteur, mais elle devrait réduire le bruit des PRs abandonnées et garder le dépôt plus propre.
- **2026-11-24** `1d027e5` CI: mark PRs idle for 3 days stale and close them after a week
- **2026-11-24** `c4d37be` D3D12.X: eliminate multi-heap UpdateTileMappings and per-texture Signal stalls in vram::Pages
- **2026-11-24** `4e3515e` D3D12.X: an ExecuteIndirect reads the arguments written before it
- **2026-11-24** `177cd40` D3D12.X: promote R9G9B9E5_SHAREDEXP render/UAV targets to R11G11B10_FLOAT
- **2026-11-24** `266b845` Direct DXIL: image_get_lod accepts static samplers
- **2026-11-24** `3bc7143` D3D12.X: eliminate multi-heap UpdateTileMappings and per-texture Signal stalls in vram::Pages
  Le commit réorganise la gestion des tuiles VRAM en conservant les allocations contiguës au sein d’un même heap, ce qui supprime les blocages liés aux appels multi‑heap `UpdateTileMappings` et aux signaux par texture. Cette amélioration poursuit le chantier d’optimisation du backend D3D12X, en suivant les précédents travaux sur le per‑lane DXIL et la réduction du IR. Désormais les mappings de tuiles sont beaucoup plus rapides (≈22× au soumission, ≈14× au rendu), mais les limitations de shaders volumineux et les optimisations SSA restent inchangées.
- **2026-11-24** `7f7e768` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
- **2026-11-24** `bfb8133` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
- **2026-11-24** `9dcf2f6` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
- **2026-11-24** `66879ef` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
- **2026-11-24** `9e27370` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
- **2026-11-24** `4e057cb` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
- **2026-11-24** `f251762` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
  Le nouveau commit retire le blocage qui refusait les fenêtres mémoire provenant du heap dans le mode per‑lane du recompiler DXIL. Ainsi `compute_with` accepte désormais `XCL_HEAP_WINDOWS`, ce qui prolonge la prise en charge déjà introduite pour le backend direct DXIL. Le changement s’inscrit dans la continuité du chantier d’optimisation du per‑lane form et de l’intégration des fenêtres mémoire, et permet à quelques programmes compute de s’exécuter avec les fenêtres du heap, même si les gains de taille restent négligeables. Les gros shaders restent encore refusés pour d’autres raisons structurelles.
- **2026-11-24** `29eccc0` Recompiler: a per-lane form for the direct DXIL backend (XCL_PER_LANE)
- **2026-11-24** `e9e94ce` Recompiler: oversized structured code falls back to pc blocks (#1307)
  Le commit introduit une limite `STRUCTURED_BYTES_PER_CODE_BYTE` (128) ; lorsqu’un shader structuré dépasse ce ratio, le traducteur refait la traduction en mode `pc` (bloc dispatcher). Cette modification vient résoudre le problème de shaders gigantesques (ex. 148 Mo) qui explosaient le IR et la compilation, poursuivant la série d’optimisations du recompiler. Désormais les programmes excessivement gros sont automatiquement re‑routés vers la forme `pc`, ce qui réduit fortement leur taille en HLSL et évite les surcoûts de compilation, tout en conservant le comportement précédent pour les shaders plus modestes.
- **2026-11-24** `ea683a0` Scripts: smoke blocks name the game folder by a hash, not its name
  Le nouveau commit modifie le script de test `smoke.ps1` afin que la ligne `title:` contienne un hash SHA‑256 de 8 caractères du nom du dossier du jeu, ou une étiquette fournie via `-Title`, masquant ainsi les noms réels des copies de jeux dans les blocs PR. Cette évolution poursuit le chantier d’amélioration des outils de CI en protégeant la confidentialité des utilisateurs, sans toucher au code du moteur ou aux backends. Désormais les blocs `xcl-smoke` sont anonymes, mais cela n’affecte pas les fonctionnalités de compilation ou d’exécution du projet. Aucun impact direct sur les limitations techniques (géométrie, textures, etc.) n’est introduit.
- **2026-11-24** `4178e88` dxil: eliminate giant-shader IR and CFG blowup in FormatLoad and multi-window memory
- **2026-11-24** `0b64aa0` dxil: eliminate giant-shader IR and CFG blowup in FormatLoad and multi-window memory
- **2026-11-24** `b9b6ea6` DirectStorage: decode eligible file requests in parallel
  Le commit ajoute une option `XCL_DSTORAGE_PARALLEL_DECOMPRESS=1` qui active un pool de travailleurs (quatre par défaut) et un budget de 32 MiB pour décompresser en parallèle les requêtes DirectStorage zlib à destination virtuelle. Cette fonctionnalité s’inscrit dans la série d’améliorations récentes du backend Direct DXIL, mais cible maintenant le sous‑système DirectStorage plutôt que le traducteur shader. Elle permet désormais de gagner jusqu’à 2 × le débit de décompression pour les petits flux compressés, tout en conservant l’ordre de retrait des requêtes; les performances sur de gros flux restent proches de l’ancien comportement.
- **2026-11-24** `f3242b2` Recompiler: split-form geometry keeps the ES and GS phases apart in programs with branches
- **2026-11-24** `85e3826` Shaders: speed up translated pipeline compiles and add PSO measurements
- **2026-11-24** `6c89285` D3D12.X: an ExecuteIndirect dispatch of an untranslated compute pipeline is skipped, as Dispatch is
- **2026-11-24** `7ccf774` kernelx: Sleep does not count the time a bind waits for a pipeline build
