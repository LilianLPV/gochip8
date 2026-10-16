# 🎮 CHIP-8 en 30 jours — un émulateur en Go

Défi personnel : construire un émulateur **CHIP-8** complet en **Go**, un petit pas chaque jour pendant 30 jours.
À la fin : un binaire qui fait tourner de vrais jeux rétro (Pong, Tetris, Space Invaders…), avec son, débogueur et désassembleur.

## Qu'est-ce que CHIP-8 ?

CHIP-8 est une machine virtuelle des années 70, conçue pour faciliter la création de jeux. C'est le « Hello World » de l'émulation :

| Composant | Détail |
|---|---|
| Mémoire | 4 Ko (4096 octets), les programmes démarrent à `0x200` |
| Registres | 16 registres 8 bits `V0`–`VF` (`VF` sert de drapeau), un registre d'index `I` 16 bits, un compteur `PC` |
| Pile | 16 niveaux, pour les appels de sous-routines |
| Minuteurs | `delay` et `sound`, décrémentés à 60 Hz |
| Écran | 64 × 32 pixels monochromes, dessin par XOR |
| Clavier | 16 touches hexadécimales (`0`–`F`) |
| Instructions | ~35 opcodes de 2 octets |

## Structure du projet

```
chip8/
├── cmd/chip8/main.go        ← point d'entrée, flags CLI
├── internal/
│   ├── cpu/                 ← mémoire, registres, fetch/decode/execute
│   │   ├── cpu.go
│   │   ├── opcodes.go
│   │   └── cpu_test.go
│   ├── display/             ← rendu (Ebitengine)
│   ├── input/               ← mapping clavier
│   └── audio/               ← le bip
├── roms/                    ← ROMs de test et jeux (vérifier les licences)
├── JOURNAL.md               ← ce que j'ai appris chaque jour
└── README.md
```

Mapping du clavier (disposition classique, adaptée en AZERTY si besoin) :

```
CHIP-8          Clavier
1 2 3 C         1 2 3 4
4 5 6 D   →     A Z E R
7 8 9 E         Q S D F
A 0 B F         W X C V
```

---

## 📅 Le programme

### Semaine 1 — Les fondations : afficher le logo IBM

- [ ] **Jour 1** — Initialiser le repo et le module Go (`go mod init`), créer l'arborescence. Lire le guide de Tobias Langhoff en entier sans coder.
- [ ] **Jour 2** — Créer la struct `CPU` : mémoire `[4096]byte`, registres `[16]byte`, `I`, `PC`, pile, minuteurs. Écrire une fonction `New()` et un premier test.
- [ ] **Jour 3** — Charger la police (les 16 caractères hexadécimaux) à `0x050`, et une ROM depuis un fichier à `0x200`. Gérer l'erreur d'une ROM trop grosse.
- [ ] **Jour 4** — **Fetch** : lire 2 octets en big-endian, avancer `PC` de 2. **Decode** : extraire les nibbles `X`, `Y`, `N`, `NN`, `NNN` avec des masques et décalages. Tests unitaires.
- [ ] **Jour 5** — Ajouter le framebuffer `[64*32]bool` et une fonction qui l'affiche en ASCII dans le terminal (outil de debug précieux).
- [ ] **Jour 6** — Premiers opcodes : `00E0` (effacer l'écran), `1NNN` (saut), `6XNN` (charger), `7XNN` (ajouter), `ANNN` (charger `I`).
- [ ] **Jour 7** — `DXYN` : dessiner un sprite en XOR, mettre `VF` à 1 en cas de collision. 🏁 **Jalon : le logo IBM s'affiche dans le terminal.**

### Semaine 2 — Une vraie fenêtre et les instructions

- [ ] **Jour 8** — Installer **Ebitengine**, ouvrir une fenêtre et afficher le framebuffer agrandi (×10).
- [ ] **Jour 9** — Boucle principale : ~700 instructions/seconde, minuteurs décrémentés à 60 Hz, indépendamment de la vitesse CPU.
- [ ] **Jour 10** — Sous-routines : `2NNN` (appel) et `00EE` (retour). Gérer le dépassement de pile.
- [ ] **Jour 11** — Sauts conditionnels : `3XNN`, `4XNN`, `5XY0`, `9XY0`.
- [ ] **Jour 12** — Logique : `8XY0`, `8XY1`, `8XY2`, `8XY3` (OR, AND, XOR).
- [ ] **Jour 13** — Arithmétique : `8XY4` (retenue), `8XY5` et `8XY7` (emprunt). Attention : `VF` doit être écrit **après** le résultat.
- [ ] **Jour 14** — Décalages `8XY6` et `8XYE`. 🏁 **Jalon : passer les tests `corax+` et `flags` de la suite de Timendus.**

### Semaine 3 — Clavier, minuteurs, son

- [ ] **Jour 15** — `BNNN` (saut avec décalage) et `CXNN` (aléatoire avec masque).
- [ ] **Jour 16** — Clavier : mapping des 16 touches, opcodes `EX9E` et `EXA1`.
- [ ] **Jour 17** — `FX0A` : bloquer jusqu'à ce qu'une touche soit **relâchée**.
- [ ] **Jour 18** — Minuteurs : `FX07`, `FX15`, `FX18`, et `FX1E` (ajouter à `I`).
- [ ] **Jour 19** — `FX29` (adresse du caractère de police) et `FX33` (décimal codé binaire, BCD).
- [ ] **Jour 20** — `FX55` et `FX65` (sauvegarde/chargement des registres). 🏁 **Jalon : tous les opcodes sont implémentés, passer `keypad` et `quirks`.**
- [ ] **Jour 21** — Le son : un bip (onde carrée) via le module audio d'Ebitengine tant que le minuteur `sound` est > 0.

### Semaine 4 — En faire un vrai logiciel

- [ ] **Jour 22** — Interface en ligne de commande avec le package `flag` : chemin de la ROM, vitesse, échelle, couleurs.
- [ ] **Jour 23** — Les *quirks* : rendre configurables les comportements qui diffèrent selon les machines (décalages, incrément de `I`, `BNNN`, découpage des sprites). Profils `chip8` / `schip`.
- [ ] **Jour 24** — Contrôles : pause, reset, exécution pas à pas.
- [ ] **Jour 25** — Débogueur en surimpression : registres, `PC`, `I`, opcode en cours, pile.
- [ ] **Jour 26** — Désassembleur : `chip8 disasm jeu.ch8` affiche le code lisible (`LD V0, 0x0A`…).
- [ ] **Jour 27** — Tests pilotés par tables (*table-driven tests*) pour chaque opcode, mesurer la couverture avec `go test -cover`.
- [ ] **Jour 28** — Jouer ! Pong, Tetris, Space Invaders, Breakout… et corriger les bugs découverts.
- [ ] **Jour 29** — GitHub Actions : tests à chaque push, compilation croisée Linux / macOS / Windows.
- [ ] **Jour 30** — Finitions : GIF de démo dans ce README, nettoyage du code, publication de la **v1.0.0**. 🎉

### 🚀 Bonus (après les 30 jours)

- Support **SUPER-CHIP** (écran 128 × 64, défilement)
- Version **WebAssembly** jouable dans le navigateur (Ebitengine le permet)
- Sauvegarde et chargement d'état
- Support de **XO-CHIP** (couleurs, meilleur son)

---

## ▶️ Lancer le projet

```bash
go run ./cmd/chip8 roms/ibm-logo.ch8
go test ./...
```

## 📚 Ressources

- [Guide to making a CHIP-8 emulator](https://tobiasvl.github.io/blog/write-a-chip-8-emulator/) — Tobias V. Langhoff (le meilleur point de départ, sans code)
- [Cowgod's Chip-8 Technical Reference](http://devernay.free.fr/hacks/chip8/C8TECH10.HTM) — la référence des opcodes
- [CHIP-8 test suite](https://github.com/Timendus/chip8-test-suite) — Timendus (ROMs de test indispensables)
- [Ebitengine](https://ebitengine.org) — moteur 2D en Go pour la fenêtre, le clavier et le son

## 📝 Règles du défi

1. Un commit minimum par jour, avec un message clair (`jour-07: implémente DXYN`).
2. Une entrée dans `JOURNAL.md` : ce que j'ai fait, ce que j'ai appris, ce qui m'a bloqué.
3. Pas de copier-coller d'un autre émulateur : lire les specs, comprendre, coder.
4. Si un jour est raté, on reprend le lendemain sans culpabiliser.