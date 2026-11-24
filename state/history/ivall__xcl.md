# Historique : ivall/xcl

## État du projet

Après ce changement, le système de scratch buffer fonctionne correctement : chaque nouvelle liste démarre sans tampon hérité, et les tampons en cours d’utilisation restent en mémoire jusqu’à ce que les fences signalent la fin de leur emploi. Le pipeline de compilation asynchrone « wanted » continue de lancer jusqu’à six jobs parallèles et le cache disque des pipelines compute reste actif. Les copies intra‑fenêtre sont toujours routées via le scratch buffer avec le budget global de 64 MiB, mais sans risque de réutilisation prématurée. Les priorités restantes (pipelines de maillage, formats GFX10, validation Windows, textures tuilées) restent à traiter, et la prochaine étape probable reste l’extension de la structuration aux shaders de pixel.

## Journal (du plus récent au plus ancien)

- **2026-11-24** `930e8b4` D3D12.X: a new command list takes no copy scratch of an earlier one
  Le commit empêche qu’une nouvelle liste de commandes réutilise le tampon scratch de copie d’une liste précédente dont la libération n’a pas été détectée, en libérant ce tampon dès la création de la nouvelle liste et en le conservant seulement jusqu’à la fin des fences. Cela poursuit les correctifs autour du bug #993 et du mécanisme de scratch buffer introduits précédemment. Désormais les listes ne partagent plus de scratch unsafe, ce qui évite les corruptions GPU ; les autres problèmes (maillage, formats GFX10, etc.) restent inchangés.
- **2026-11-24** `010e1ec` D3D12.X: a copy within one memory window reads through a scratch buffer (#958)
  Le commit réactive le chemin de copie via un tampon scratch lorsque la source et la destination partagent la même fenêtre de mémoire unifiée, évitant l’erreur de debug‑layer 538. Cette correction poursuit la série de correctifs autour du bug #958 déjà amorcée par les précédents commits sur le scratch buffer. Désormais les copies intra‑fenêtre sont sûres et logguées, mais les autres blocages (pipelines de maillage, formats GFX10, validation Windows) restent inchangés.
- **2026-11-24** `b8b1f49` Recompiler: jumps to outer loop heads and into loops are structured
  Le nouveau commit ajoute un test exhaustif qui génère aléatoirement des programmes contenant des sauts complexes (jumps vers les têtes de boucles externes, continues, breaks, sauts dans des boucles) et vérifie que la traduction « structured » produit le même état que le dispatcher de blocs. Cela complète la série de correctifs « demand » en s’assurant que les structures de contrôle compliquées sont correctement reconnues comme structurées, éliminant ainsi le besoin du dispatcher lent. Le résultat montre que la plupart des programmes (≥ 100/120) sont désormais traités comme structurés, ce qui réduit fortement le temps de compilation du driver pour ces cas. Aucun nouveau blocage n’est introduit, mais la validation reste limitée aux shaders de calcul ; les problèmes de pipelines de maillage et de formats GFX10 restent inchangés.
- **2026-11-24** `9a272d4` Recompiler: a DXC compile the title waits for does not queue for memory
  Le commit ajoute la notion de « wanted » aux jobs de compilation DXC et modifie la fonction d’attente mémoire pour lancer immédiatement les compilations dont le titre attend le résultat, jusqu’à six en parallèle. Cette évolution poursuit la série de correctifs autour du mécanisme « demand » introduit précédemment, en réduisant les files d’attente lorsque la mémoire disponible est faible. Elle permet ainsi de diminuer les temps d’attente pendant le chargement de niveaux, même si le watchdog d’arrêt du titre continue de se déclencher. Le reste du pipeline reste inchangé.
- **2026-11-24** `25c6f7e` D3D12.X: a big pipeline build the title waits for does not wait for its turn
  Le commit introduit un mécanisme « demand » qui détecte lorsqu’un thread du jeu attend la construction d’un pipeline compute volumineux et force le worker en arrière‑plan à poursuivre immédiatement, en augmentant sa priorité si nécessaire. Cette évolution poursuit la série de correctifs autour du cache disque et du scratch buffer en résolvant le blocage où le titre pouvait rester bloqué pendant que le worker attendait de la mémoire commit. Désormais les builds de gros pipelines ne sont plus mis en attente indéfiniment, ce qui évite les hangs du watchdog; les tests qui échouaient sans ce changement passent maintenant.
- **2026-11-24** `6cef69f` Shaders: save offline compute pipelines to the runtime driver cache
  Le nouveau commit ajoute la mise en cache disque des blobs de pipelines compute générés par le driver. Il s’inscrit dans la série de correctifs visant à stabiliser le backend D3D12.X en réduisant les recompilations inutiles, après les améliorations du scratch buffer et du VFS. Désormais les pipelines compute peuvent être réutilisés entre deux exécutions XCL, ce qui élimine le besoin de recompilation à chaque lancement, mais les problèmes de formats d’image et de maillage restent inchangés.
- **2026-11-24** `392dc0c` D3D12.X: a copy within one memory window reads through a scratch buffer (#958)
  Le commit introduit une limitation de 64 MiB pour les tampons de secours (scratch) utilisés lors des copies intra‑fenêtre, en conservant un tampon par liste d’enregistrement et en le libérant seulement après la complétion des fences. Cette évolution poursuit la série de correctifs autour du mécanisme de scratch initiée par le PR #958, en ajoutant une gestion de budget et de journalisation. Désormais les copies qui partagent la même fenêtre de mémoire passent par le scratch sans dépasser le budget, ce qui élimine l’erreur 538 du debug layer tout en conservant le chemin direct comme secours. Il reste à vérifier l’impact de ce budget sur les scénarios de copies très volumineuses et sur les builds Windows, qui n’ont pas encore été testés.
- **2026-11-24** `21bc548` D3D12.X: a copy within one memory window reads through a scratch buffer (#958)
  Le commit introduit un tampon de secours (scratch) pour les copies `CopyMemoryToMemoryX` dont la source et la destination se trouvent dans la même fenêtre de mémoire unifiée. Au lieu d’essayer de copier directement – ce qui violait les exigences d’état D3D12 et déclenchait l’erreur 538 du debug layer – les octets sont d’abord copiés dans ce tampon temporaire, puis vers la destination. Cette amélioration s’inscrit dans la série de correctifs récents sur la gestion des ressources D3D12 (allocateurs, états de format, etc.) et élimine le blocage du debug‑layer sur les copies intra‑fenêtre, tout en conservant le chemin de copie direct lorsqu’aucun scratch n’est disponible.
- **2026-11-24** `47efa2e` VFS: reject removal of non-empty virtual directories
  Le commit ajoute une vérification que le répertoire original est vide avant d’autoriser sa suppression dans le VFS, en filtrant les entrées masquées (whiteouts). Cette logique complète les précédents travaux sur la gestion des suppressions d’overlay en empêchant la perte de données visibles, prolongeant ainsi le chantier de robustesse du système de fichiers. Désormais, la couche refuse de supprimer des dossiers contenant des fichiers ou sous‑dossiers non masqués, ce qui corrige le bug #997 mais ne touche pas aux autres limitations (pipelines de maillage, formats d’image).
- **2026-11-24** `d78597d` Tests: truncate_existing_validation_errors reads the last error before under_wine()
- **2026-11-24** `24d8b92` D3D12.X: a format without tiled support is not reservable, whatever the MSAA query says
- **2026-11-24** `6828e07` Merge PR #951
- **2026-11-24** `dc6a283` Merge PR #1037
- **2026-11-24** `a610eb5` Merge PR #990
- **2026-11-24** `384f7f7` Merge PRs #1009, #1012, #1023 (allocator pool: failed Resets, released lists)
- **2026-11-24** `9a19d7e` PR checks: reject untouched PR template placeholders (#965)
- **2026-11-24** `01d4c77` D3D12.X: move allocator recordings only after successful Reset
- **2026-11-24** `b55c478` D3D12.X: stop released lists holding allocator recordings
- **2026-11-24** `f52ec36` D3D12.X: keep reusable allocators after a failed Reset
- **2026-11-24** `67a3735` Merge PR #905
- **2026-11-24** `e74dda2` Merge PR #995
- **2026-11-24** `10915eb` Tests: BCPack lanes against independent synthetic fixtures, models against the patent
- **2026-11-24** `8add9a3` DirectStorage: BCPack provenance, README row, one Request::check again
- **2026-11-24** `8cad517` DirectStorage: unpack BCPack BC1, BC3, BC4, BC5, BC6H and BC7 streams
- **2026-11-24** `6efdabc` Merge PR #988
- **2026-11-24** `ef48a18` PR checks: build inputs outside src need a regression run
- **2026-11-24** `02bfd1d` Memory: link xcl.exe without high-entropy ASLR so title memory stays under 2^40 (PR #631)
- **2026-11-24** `afe4226` Merge PR #551
- **2026-11-24** `de1809c` Merge PR #693
- **2026-11-24** `9e80056` Merge PR #891
- **2026-11-24** `87fae1f` D3D12.X: GFX10 image format 134 (1_5_5_5) is B5G5R5A1, not 135 (#946)
- **2026-11-24** `6522c24` PR checks: two xcl-smoke blocks in one code fence are read as two
- **2026-11-24** `9aec53d` Merge fix: a lazy page's move keeps map_pages' runs (#556 with #849)
- **2026-11-24** `3ca8566` Merge PR #556
- **2026-11-24** `236e1a3` Merge PR #532
- **2026-11-24** `6bac4b4` Merge PR #849
- **2026-11-24** `6922eaf` Merge PR #923
- **2026-11-24** `38ea451` D3D12.X: ask for tiled support before making a GPU-owned texture reserved (#940)
