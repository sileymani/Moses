# Répartition détaillée du travail — projet ISTA/FISTA

> À lire par toute l'équipe. Ce document explique, à partir du **dépôt Git** et du **notebook `projet_ista_fista.ipynb`**, ce que chacun doit produire concrètement, comment, et dans quel ordre. Le notebook est le **fil narratif commun** ; chaque membre branche sa contribution sur la trame existante.

---

## 0. Démarrage commun (tout le monde, 10 min)

Avant toute chose, chacun met en place l'environnement :

```bash
git clone <url-du-repo>
cd <repo>
python -m venv .venv
# Windows :
.venv\Scripts\activate
# macOS / Linux :
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name=projet-convex
```

Puis : ouvrir `projet_ista_fista.ipynb`, **sélectionner le kernel `projet-convex`**, exécuter la **Cellule 0**. Si elle affiche `venv actif : oui`, l'environnement est bon. Lire ensuite le notebook en entier une fois : c'est le socle dont tout le monde part.

**Règles communes (le socle) :**
- On réutilise les fonctions déjà définies : `soft_threshold`, `objective`, `ista`, `fista`. On ne les réécrit pas.
- On réutilise le **style graphique** de la section 0.1 (palette, grilles) pour que toutes les figures soient homogènes.
- On respecte les **notations** du tableau commun (`x, A, y, F, f, g, L, t, λ, k, x*`).
- Chaque algo respecte la signature : `algo(A, y, lam, t, n_iter, x0=None) -> (x, history)`.
- Une branche Git par personne (`feature/theorie-prox`, `feature/preuves`, etc.), PR relue par le Membre 1 avant fusion sur `main`.

**Structure du dépôt :**
```
/                      README, requirements.txt, .gitignore (ignore .venv/)
/notebook              projet_ista_fista.ipynb  (le fil narratif)
/src                   proximal.py  (algos reutilisables, importables)
/experiments           scripts qui generent les figures du rapport
/report                sources LaTeX (un .tex par section)
/data                  signaux audio pour la section bonus (non versionnes si lourds)
```

---

## 1. Membre 1 — Cadre, socle & intégration *(= moi)*

**Rôle :** garant de la cohérence d'ensemble. Je fournis le terrain sur lequel les autres construisent.

**Livrables :**
- Le **socle** : notebook initial, `requirements.txt`, structure du repo, Cellule 0 d'environnement, style graphique, tableau de notations. *(déjà fait)*
- **Sections 1-3 du rapport** : introduction & motivation ; rappels du cours (convexité, propriété-or, somme de convexes) ; formulation du problème composite `F = f + g` et preuve de sa convexité.
- **Intégration finale** : fusion des branches, harmonisation des notations sur tout le rapport, relecture transversale, conclusion (section 10) co-écrite avec le Membre 4.

**Points d'attention :** je suis le point de contact pour toute question de notation ou d'interface. Toute PR passe par moi.

---

## 2. Membre 2 — Théorie proximale *(le pivot — à livrer en premier)*

**Rôle :** fournir la fondation théorique dont dépendent les preuves des Membres 3.

**Ce que le notebook fournit déjà :** la section 4 *admet* la formule du seuillage doux et la visualise. **Ta mission : la démontrer rigoureusement.**

**Livrables :**
- **Section théorique du rapport (≈ section 4)** :
  - Définition du **sous-différentiel** ∂g (généralisation du gradient aux fonctions non lisses) ; cas de la valeur absolue.
  - **Condition d'optimalité** : `0 ∈ ∂F(x*)` (le "gradient nul" généralisé).
  - Définition de l'**opérateur proximal** `prox_{tg}(z) = argmin_u g(u) + (1/2t)‖u−z‖²`, interprétation géométrique.
  - **Dérivation complète du seuillage doux** : montrer pas à pas que le prox de `λ‖·‖₁` se sépare coordonnée par coordonnée et donne `sign(z)·max(|z|−tλ, 0)`. C'est le cœur.
  - Propriété de **non-expansivité** du prox (utile pour les preuves de convergence).
- **Contribution notebook** : enrichir la section 4 avec une cellule markdown contenant la dérivation (ou un renvoi clair au rapport), éventuellement une figure illustrant le sous-différentiel de `|t|` en 0 (l'ensemble des pentes `[−1, 1]`).

**Dépendance :** personne ne dépend de toi *en amont*, mais **le Membre 3 dépend de toi**. À livrer tôt.

---

## 3. Membre 3 — Algorithmes & preuves de convergence

**Rôle :** transformer la théorie proximale en algorithmes justifiés, avec leurs garanties.

**Ce que le notebook fournit déjà :** les implémentations `ista` et `fista` qui tournent, et la courbe de convergence empirique. **Ta mission : expliquer *pourquoi* ils marchent et *à quelle vitesse*.**

**Livrables :**
- **Section ISTA (≈ section 5 du rapport)** :
  - Dérivation d'ISTA comme **majoration-minimisation** : à chaque pas on minimise un majorant quadratique de `f` + le terme `g`. Montrer que ça redonne exactement « pas de gradient + prox ».
  - Rôle du pas `t ≤ 1/L` et de la constante de Lipschitz `L = ‖AᵀA‖`.
  - **Preuve de convergence en `O(1/k)`** (argument du gradient Lipschitz).
- **Section FISTA (≈ section 6)** :
  - Le **momentum de Nesterov** : d'où vient l'extrapolation `y_{k+1} = x_k + ((t_k−1)/t_{k+1})(x_k − x_{k-1})`.
  - **Preuve (ou esquisse détaillée) de la convergence en `O(1/k²)`** (suite d'énergie de Beck & Teboulle, 2009).
  - Discussion des **oscillations** observées et de la non-monotonie ; mention des variantes *restart*.
- **Contribution notebook/code** : si besoin, ajouter au `src/proximal.py` une variante (ex. FISTA monotone ou avec restart) et l'illustrer dans le notebook en réutilisant la trame de la section 7.

**Dépendance :** tu as besoin de la **section 4 du Membre 2** (prox, optimalité). Préviens-le que tu attends sa livraison.

---

## 4. Membre 4 — État de l'art, expériences & application audio

**Rôle :** situer ISTA/FISTA dans le paysage, produire les expériences chiffrées, et réaliser l'application bonus.

**Ce que le notebook fournit déjà :** un problème synthétique, une courbe de convergence, le style graphique. **Ta mission : étendre les expériences et comparer aux alternatives.**

**Livrables :**
- **État de l'art (≈ section 7 du rapport)** — exigé par les critères du Sujet B (« comparer les alternatives ») :
  - Tableau comparatif : ISTA, FISTA, **coordinate descent**, **ADMM**, **proximal Newton**.
  - Pour chacun : principe, vitesse de convergence, coût par itération, quand le préférer.
  - Discussion des compromis (mémoire, robustesse, structure du problème).
- **Expériences (≈ section 9)** :
  - Validation sur signal synthétique (déjà amorcée dans le notebook) : qualité de récupération vs `k` vrais coefficients.
  - **Étude de sensibilité** : effet de `λ` (parcimonie vs fidélité) et du pas `t` sur la convergence.
  - Courbes propres, réutilisant le style commun, exportées dans `/experiments` pour le rapport.
- **Application audio (≈ section 8, bonus)** :
  - Pipeline : signal → représentation temps-fréquence (STFT) → LASSO résolu par FISTA → reconstruction.
  - Métrique **SDR** (Signal-to-Distortion Ratio), écoute avant/après.
  - Nécessite `librosa` (dépendance optionnelle ; `pip install librosa`).
- **Conclusion (section 10)** co-écrite avec le Membre 1.

**Dépendance :** tu peux **commencer l'état de l'art immédiatement** (indépendant). Les expériences avancées et l'audio réutilisent `ista`/`fista` (déjà disponibles) ; l'audio gagne à attendre une éventuelle variante du Membre 3 mais n'en dépend pas strictement.

---

## 5. Ordre de travail & chemin critique

```
Membre 1 : socle  ──────────────►  débloque tout le monde
                                         │
        ┌────────────────────────────────┼────────────────────────────┐
        ▼                                 ▼                            ▼
Membre 2 : theorie prox            Membre 1 : sections 1-3      Membre 4 : etat de l'art
(section 4) -- A LIVRER TOT                                     (demarre tout de suite)
        │
        ▼
Membre 3 : ISTA/FISTA + preuves (sections 5-6)
        │
        ▼
Membre 4 : experiences avancees + audio (sections 8-9)
        │
        ▼
Membre 1 : integration, relecture, conclusion (section 10)  ──►  RENDU
```

**Chemin critique :** Membre 2 → Membre 3 → Membre 4 (audio). Donc le Membre 2 ne doit pas tarder, et le Membre 3 enchaîne dès que la section 4 est prête. Le Membre 4 occupe ce temps avec l'état de l'art, qui est indépendant.

---

## 6. Jalons (à caler sur le calendrier réel, deadline 7 juin 23h59)

| Jalon | Contenu | Responsable |
|---|---|---|
| J0 | Socle + repo + notebook diffusés | M1 ✅ |
| J1 | Section 4 (prox + dérivation seuillage doux) | M2 |
| J2 | Sections 5-6 (ISTA/FISTA + preuves) | M3 |
| J3 | État de l'art + étude de sensibilité | M4 |
| J4 | Sections 1-3 + application audio | M1, M4 |
| J5 | Intégration, relecture croisée, conclusion | tous |
| J6 | Rendu (rapport 10-20 p. + code) | M1 |

---

## 7. Check-list qualité avant le rendu

- [ ] Le notebook s'exécute **de bout en bout** sans erreur sur un venv propre (Cellule 0 → fin).
- [ ] Toutes les figures utilisent le **style commun**.
- [ ] Les **notations** sont cohérentes entre notebook et rapport.
- [ ] Le rapport fait **10 à 20 pages** et met l'**analyse des techniques d'optimisation au premier plan** (critère n°1).
- [ ] L'**état de l'art** compare bien les alternatives (critère n°2).
- [ ] Le **code** est propre, commenté, importable depuis `/src` (critère n°3).
- [ ] FISTA/ISTA restent le **cœur** ; l'audio est présenté comme **application bonus**.
