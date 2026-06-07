# Projet Optimisation Convexe — ISTA / FISTA

Méthodes de gradient proximal pour résoudre un problème convexe non lisse (LASSO).
Le notebook `Notebook.ipynb` est le fil narratif du projet.

> ⚠️ **À lire avant de lancer le notebook.** Suivre les étapes ci-dessous une seule fois met en place l'environnement partagé. Sans ça, le notebook risque de ne pas trouver les bibliothèques (ou d'utiliser le mauvais Python).

---

## 1. Récupérer le projet

```bash
git clone https://github.com/sileymani/Moses.git
cd Moses
```

## 2. Créer l'environnement virtuel (venv)

On utilise un venv pour que tout le monde travaille avec les **mêmes versions** de bibliothèques.

```bash
python -m venv .venv
```

Cela crée un dossier `.venv/` local (déjà ignoré par Git, voir section 6).

## 3. Activer le venv

**Windows (PowerShell)**
```powershell
.venv\Scripts\Activate.ps1
```

**Windows (cmd)**
```cmd
.venv\Scripts\activate.bat
```

**macOS / Linux**
```bash
source .venv/bin/activate
```

Une fois activé, l'invite de commande affiche `(.venv)` au début de la ligne.

## 4. Installer les bibliothèques

```bash
pip install -r requirements.txt
```

Contenu de `requirements.txt` :

| Bibliothèque | Rôle |
|---|---|
| `numpy` | calcul vectoriel/matriciel (cœur des algos) |
| `scipy` | utilitaires numériques |
| `matplotlib` | figures et courbes de convergence |
| `librosa` | traitement audio — **seulement pour l'application bonus** |
| `notebook` | exécuter le notebook Jupyter |
| `ipykernel` | enregistrer le venv comme kernel (étape 5) |

## 5. Enregistrer le venv comme kernel Jupyter

Pour que le notebook s'exécute **dans ce venv** (et pas dans le Python système) :

```bash
python -m ipykernel install --user --name=projet-convex
```

## 6. Lancer le notebook

```bash
jupyter notebook
```

Ouvrir `projet_ista_fista.ipynb`, puis **sélectionner le kernel `projet-convex`** :
*menu Kernel → Change kernel → projet-convex* (ou en haut à droite de l'interface).

Exécuter les cellules dans l'ordre, du début à la fin.

---

## Notes importantes

### À refaire à chaque session de travail
Le venv doit être **réactivé** à chaque nouveau terminal (étape 3). La création (étape 2) et l'installation (étapes 4-5) ne se font qu'une fois.

### Le kernel ≠ l'activation du terminal
Activer le venv dans le terminal sert pour `pip` et `jupyter`. Mais c'est le **kernel sélectionné dans le notebook** qui détermine quel Python exécute les cellules. Bien penser à choisir `projet-convex` (étape 6), sinon les bibliothèques installées dans le venv ne seront pas trouvées.

### Application audio (bonus)
`librosa` n'est nécessaire que pour la section d'application audio. Si l'installation pose problème sur ta machine, tu peux travailler sur tout le reste du notebook sans elle.

### Git — ne pas versionner le venv
Le dossier `.venv/` ne doit **jamais** être poussé sur Git (il est volumineux et spécifique à chaque machine). Vérifier que le `.gitignore` contient :

```gitignore
.venv/
__pycache__/
*.pyc
.ipynb_checkpoints/
```

### Mettre à jour les dépendances
Si quelqu'un ajoute une bibliothèque, il met à jour `requirements.txt` puis le signale à l'équipe. Les autres relancent simplement :

```bash
pip install -r requirements.txt
```

---

## Dépannage rapide

| Problème | Cause probable | Solution |
|---|---|---|
| `ModuleNotFoundError` dans une cellule | mauvais kernel sélectionné | choisir le kernel `projet-convex` (étape 6) |
| `(.venv)` n'apparaît pas | venv non activé | refaire l'étape 3 |
| Le kernel `projet-convex` n'apparaît pas dans Jupyter | étape 5 oubliée | relancer `python -m ipykernel install --user --name=projet-convex` |
| `librosa` ne s'installe pas | dépendances système audio | l'ignorer si tu ne fais pas la partie audio |
