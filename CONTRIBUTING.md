# Contribuer à gochip8

## Convention de commits

Ce projet suit la spécification [Conventional Commits](https://www.conventionalcommits.org/fr/v1.0.0/).

### Format

```
type(scope): description courte
```

- **type** : la nature du changement (voir la liste ci-dessous)
- **scope** : la partie du projet concernée (optionnel)
- **description** : à l'impératif, en minuscules, sans point final, 72 caractères maximum

### Types

| Type | Usage | Exemple |
|---|---|---|
| `feat` | nouvelle fonctionnalité | `feat(cpu): implémente l'opcode DXYN` |
| `fix` | correction de bug | `fix(cpu): corrige VF dans 8XY5` |
| `test` | ajout ou modification de tests | `test(cpu): ajoute les tests de decode` |
| `docs` | documentation, README, journal | `docs(journal): ajoute le jour 3` |
| `refactor` | réorganisation sans changer le comportement | `refactor(display): extrait le rendu` |
| `perf` | amélioration des performances | `perf(display): évite de redessiner l'écran inchangé` |
| `style` | formatage, sans changement de logique | `style: applique gofmt` |
| `chore` | configuration, dépendances, outils | `chore: ajoute la dépendance ebitengine` |
| `ci` | intégration continue | `ci: ajoute les tests automatiques` |

### Scopes

| Scope | Partie du projet |
|---|---|
| `cpu` | `internal/cpu` — mémoire, registres, opcodes |
| `display` | `internal/display` — rendu graphique |
| `input` | `internal/input` — clavier |
| `audio` | `internal/audio` — son |
| `cli` | `cmd/chip8` — ligne de commande |
| `journal` | `JOURNAL.md` |
| `readme` | `README.md` |

### Corps du message (optionnel)

Pour un changement qui mérite une explication, ajouter une ligne vide puis un corps :

```
fix(cpu): écrit VF après le résultat dans 8XY4

Si X vaut F, écrire le drapeau avant le résultat l'écrasait.
Corrige le test flags de la suite de Timendus.
```

### Changement cassant

Ajouter un `!` après le type, ou une ligne `BREAKING CHANGE:` dans le corps :

```
feat(cli)!: remplace l'argument --rom par un argument positionnel
```

## Avant chaque commit

```bash
gofmt -w .
go vet ./...
go test ./...
```

## Règles

1. Un commit = un changement logique. Mieux vaut plusieurs petits commits qu'un gros.
2. Le code compile et les tests passent à chaque commit.
3. Les messages de commit sont en français.