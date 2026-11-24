# Historique : ivall/xcl

## État du projet

Après ce changement, le VFS empêche la suppression de répertoires non vides tant que des enfants visibles existent, tout en conservant le comportement de whiteout pour les entrées déjà masquées. Les tests existants ont été mis à jour pour couvrir ces nouveaux cas et passent, renforçant la fiabilité du système de fichiers. Le reste du projet reste inchangé : le backend D3D12.X continue de se stabiliser, les shaders et la mémoire fonctionnent, mais les pipelines de maillage et certains formats d’image restent des points bloquants. La prochaine étape probable sera de valider ces nouvelles règles sur Windows (les tests Windows sont encore en attente) et de poursuivre les correctifs liés aux textures tuilées et aux formats GFX10.

## Journal (du plus récent au plus ancien)

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
