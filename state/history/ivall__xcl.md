# Historique : ivall/xcl

## État du projet

Après ce changement, la documentation continue d’être correctement rendue, avec les badges et le tableau d’avancement du jeu retail affichés sans artefacts. Les backends DXIL, le fallback `pc`, le logging SSA, le pool DirectStorage SIMD et `TilePool` restent opérationnels, et les tests unitaires ainsi que les tests GPU sont verts. Le seul point modifié est le lien Discord, désormais à jour, tandis que les goulots de performance du per‑lane form et la complétion du pipeline retail restent les prochains défis à relever. La prochaine étape probable reste l’optimisation du pipeline retail.

## Journal (du plus récent au plus ancien)

- **2026-11-25** `6e1e2be` Update Discord link in README.md
  Le commit remplace l’URL du badge Discord dans le README, passant de `discord.gg/D7fAUQDRyd` à `discord.gg/xcl`. Cette modification poursuit le petit chantier de ménage de la documentation déjà amorcé, où l’on corrige les liens et le formatage du README. Elle ne touche aucun code ni aucune fonctionnalité du moteur XCL. Ainsi, l’accès au serveur Discord devient plus fiable, mais les performances et le pipeline retail restent inchangés.
- **2026-11-25** `6020a33` README: the requirements' DXC line is not a list item
  Le commit corrige le formatage d’une ligne du README où la mention du fichier DXC était précédée d’un « + », ce qui faisait apparaître le texte comme un élément de liste. Cette modification s’inscrit dans la même vague de ménage de la documentation déjà amorcée (corrections de liens Discord, ajout de page d’accueil). Elle n’affecte aucun code et ne change donc pas les fonctionnalités existantes, mais améliore la lisibilité du guide de construction.
- **2026-11-25** `bb35b45` README: the requirements' DXC line is not a list item
  Le commit corrige le rendu Markdown du README en retirant le « + » qui faisait de la ligne DXC un élément de liste, évitant ainsi qu’une partie des exigences apparaisse comme un bullet. Cette petite modification s’inscrit dans la série de nettoyages de la documentation déjà engagés (ajout de page d’accueil, correction du badge Discord, mise à jour de l’avancement du jeu). Elle n’affecte aucune fonctionnalité du code ; le README s’affiche désormais correctement, mais le projet reste limité par les performances et le pipeline incomplet du jeu retail.
- **2026-11-25** `f5c5bdf` README: a front page, and the newer retail game's state (about 65 %, 14 fps on average)
- **2026-11-25** `bae19e9` README: a front page, and the newer retail game's state (about 65 %, 14 fps on average)
  Le commit ajoute une page d’accueil visuelle au README : bannière, carte d’état du jeu retail (≈ 65 % / 14 fps), diagramme du fonctionnement d’XCL et étapes pour lancer un titre. C’est la suite logique du chantier de documentation amorcé précédemment (ajout d’une page frontale et correction du lien Discord) et ne touche aucun code. Désormais le dépôt présente clairement où en est le projet et comment le faire fonctionner, mais les limitations techniques (goulet de per‑lane form, pipeline incomplet) restent inchangées.
- **2026-11-25** `4b9eb9b` README: a front page, and the newer retail game's state (about 65 %, 14 fps on average)
  Le nouveau commit enrichit le README d’une page d’accueil détaillée et ajoute une « carte » indiquant l’avancement d’un jeu retail récent, estimé à 65 % avec 14 fps moyens (RTX 5090) grâce à la branche d’intégration NVIDIA. Cette mise à jour poursuit le chantier de documentation amorcé précédemment (correction du lien Discord) et ne touche aucun code. Elle rend visible, pour la première fois, un état de performance réel, mais l’accès complet au jeu reste bloqué (pipeline minimap refusé, arrêt prématuré).
- **2026-11-25** `6987e49` Fix Discord link formatting in README.md
  Le commit corrige le format du lien Discord dans le README en retirant un crochet superflu et en affichant correctement le texte cliquable. Cette modification poursuit le petit chantier de ménage de la documentation amorcé précédemment, sans impacter le code ni les fonctionnalités. Elle rend l’accès au serveur Discord plus fiable pour les contributeurs. Aucun changement fonctionnel n’est introduit, le projet reste dans la même dynamique de stabilisation.
- **2026-11-25** `c390263` Fix Discord link formatting in README.md
  Le commit corrige le formatage du lien Discord dans le README en ajustant la syntaxe markdown du badge. Cette modification poursuit le petit chantier de ménage de la documentation amorcé précédemment, sans toucher au code ni aux fonctionnalités existantes. Elle rend l’accès au serveur Discord plus fiable, mais n’apporte aucun changement fonctionnel au projet. Aucun nouveau blocage n’est introduit et les capacités précédentes restent inchangées.
- **2026-11-25** `2f27167` Fix Discord link formatting in README.md
  Le commit corrige le format du lien Discord dans le README en encapsulant correctement l’URL dans un lien markdown. Cette modification poursuit le ménage de la documentation amorcé précédemment, sans toucher au code ni aux fonctionnalités. Elle rend l’accès au serveur Discord plus fiable pour les lecteurs. Aucun nouvel obstacle n’est introduit, mais cela n’apporte pas de changement fonctionnel au projet.
- **2026-11-25** `330a365` Fix Discord link and update display text
  Le commit corrige simplement le lien et le texte du badge Discord dans le README, remplaçant l’URL erronée par la bonne. Cette modification s’inscrit dans la phase de ménage du projet, où l’on affine la documentation après avoir stabilisé les tests et les backends. Elle ne change aucune fonctionnalité du code, mais rend désormais le point de contact communautaire fiable pour les contributeurs.
- **2026-11-25** `cbc296a` D3D12.X: a huge compute program the driver refuses is logged with its verdict
- **2026-11-25** `56ec08d` Tests: verify D3D12.X resource, heap start, fence, and queue sync parity
- **2026-11-25** `aaa0936` Recompiler: direct DXIL lowers a repeating compute program's blocks once
- **2026-11-25** `5ce558f` Tests: skip half_precision_overloads_run without native 16-bit ops
- **2026-11-25** `a1a2978` Tests: the sparse VRAM test no longer depends on what ran before it
  Le nouveau commit rend les tests de VRAM sparse indépendants du contexte d’exécution en corrigeant deux bugs : les charges scalaires S_LOAD_DWORD* n’utilisaient plus d’offset de registre (SOFFSET=125) et les tests utilisent désormais l’instance backend déjà initialisée via `d3d12x::backend()` au lieu d’appeler `real::init()` directement. Cette modification poursuit le chantier de stabilisation des tests GPU, éliminant les dépendances qui provoquaient des lectures hors‑heap et des blocages sur les cartes NVIDIA 32‑lane. Le résultat est que les suites de tests peuvent s’exécuter en parallèle sans plantage, mais les performances du per‑lane form restent inchangées.
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
