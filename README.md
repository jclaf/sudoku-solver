# sudoku-solver

- **Ce code résout un Sudoku en utilisant le backtracking, une méthode systématique qui essaie toutes les possibilités et revient en arrière si nécessaire.**
- **La classe `Board` gère l’affichage, la recherche de cases vides, la validation des coups et la résolution.**
- **Le code affiche la grille avant et après résolution.**

---

## Description du Code

### 1. **Classe `Board`**

La classe `Board` encapsule la logique pour manipuler et résoudre une grille de Sudoku.

#### a. **Initialisation**

```python
def __init__(self, board):
    self.board = board
```
- **`board`** : une liste de listes (9x9) représentant la grille de Sudoku, où 0 indique une case vide.

#### b. **Affichage de la grille**

```python
def __str__(self):
    board_str = ''
    for row in self.board:
        row_str = [str(i) if i else '*' for i in row]
        board_str += ' '.join(row_str)
        board_str += '\n'
    return board_str
```
- Permet d’afficher joliment la grille.
- Les zéros sont remplacés par des `*` pour mieux visualiser les cases vides.

#### c. **Recherche d’une case vide**

```python
def find_empty_cell(self):
    for row, contents in enumerate(self.board):
        try:
            col = contents.index(0)
            return row, col
        except ValueError:
            pass
    return None
```
- Parcourt la grille pour trouver la première case vide (valeur 0).
- Retourne ses coordonnées `(row, col)` ou `None` si la grille est complète.

#### d. **Vérification de validité d’un chiffre**

- **Dans la ligne :**
  ```python
  def valid_in_row(self, row, num):
      return num not in self.board[row]
  ```
- **Dans la colonne :**
  ```python
  def valid_in_col(self, col, num):
      return all(self.board[row][col] != num for row in range(9))
  ```
- **Dans le carré 3x3 :**
  ```python
  def valid_in_square(self, row, col, num):
      row_start = (row // 3) * 3
      col_start = (col // 3) * 3
      for row_no in range(row_start, row_start + 3):
          for col_no in range(col_start, col_start + 3):
              if self.board[row_no][col_no] == num:
                  return False
      return True
  ```
- **Synthèse :**
  ```python
  def is_valid(self, empty, num):
      row, col = empty
      valid_in_row = self.valid_in_row(row, num)
      valid_in_col = self.valid_in_col(col, num)
      valid_in_square = self.valid_in_square(row, col, num)
      return all([valid_in_row, valid_in_col, valid_in_square])
  ```
- **But** : Vérifier si un chiffre peut être placé à une position donnée sans violer les règles du Sudoku.

#### e. **Résolution du Sudoku (Backtracking)**

```python
def solver(self):
    if (next_empty := self.find_empty_cell()) is None:
        return True
    for guess in range(1, 10):
        if self.is_valid(next_empty, guess):
            row, col = next_empty
            self.board[row][col] = guess
            if self.solver():
                return True
            self.board[row][col] = 0
    return False
```
- **Principe** :
    1. Cherche une case vide.
    2. Essaie tous les chiffres de 1 à 9.
    3. Pour chaque chiffre valide, le place et tente de résoudre la suite récursivement.
    4. Si aucun chiffre ne convient, revient en arrière (backtrack).
    5. Si aucune case vide n’est trouvée, la grille est résolue.

---

### 2. **Fonction de résolution globale**

```python
def solve_sudoku(board):
    gameboard = Board(board)
    print(f'Puzzle to solve:\n{gameboard}')
    if gameboard.solver():
        print(f'Solved puzzle:\n{gameboard}')
    else:
        print('The provided puzzle is unsolvable.')
    return gameboard
```
- Crée une instance de `Board`.
- Affiche la grille initiale.
- Tente de résoudre la grille et affiche le résultat ou un message d’échec.

---

### 3. **Exemple d’utilisation**

```python
puzzle = [
  [0, 0, 2, 0, 0, 8, 0, 0, 0],
  [0, 0, 0, 0, 0, 3, 7, 6, 2],
  [4, 3, 0, 0, 0, 0, 8, 0, 0],
  [0, 5, 0, 0, 3, 0, 0, 9, 0],
  [0, 4, 0, 0, 0, 0, 0, 2, 6],
  [0, 0, 0, 4, 6, 7, 0, 0, 0],
  [0, 8, 6, 7, 0, 4, 0, 0, 0],
  [0, 0, 0, 5, 1, 9, 0, 0, 8],
  [1, 7, 0, 0, 0, 6, 0, 0, 5]
]

if __name__=="__main__":
  solve_sudoku(puzzle)
```
- Résout le Sudoku donné et affiche la solution.

---

## Algorithme Utilisé

### **Backtracking (Recherche exhaustive avec retour arrière)**

- **Principe** :
    - On cherche une case vide.
    - On essaie toutes les possibilités (1 à 9) pour cette case.
    - Si une possibilité mène à une impasse, on revient en arrière et on essaie la suivante.
    - Si la grille est complète, c’est gagné !
- **Caractéristiques** :
    - **Méthode récursive**.
    - **Complexité exponentielle** dans le pire des cas, mais très efficace pour la plupart des grilles de Sudoku standards.
    - **Aucune solution** : le code détecte et affiche un message si la grille n’est pas résoluble.

---
