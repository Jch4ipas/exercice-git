# Dojo Git : survivre à un conflit

Vous travaillez sur **cafet-app**, l'app de la cafétéria.
Une personne de l'exploitation et une personne du dev ont modifié la même chose.
Git ne sait pas qui a raison : c'est à vous de décider.

> **Windows** : utilisez **Git Bash**.
>
> **Éditer un fichier** : `nano nom-du-fichier`
> puis `Ctrl+O`, `Entrée` pour sauvegarder et `Ctrl+X` pour quitter.
>
> **Perdu ?** Tapez `git status`. Git vous dit presque toujours quoi faire.
>
> **Panique ?** `git merge --abort` annule la fusion en cours.

---

## Étape 0 : préparer Git (1 min)

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "prenom.nom@exemple.ch"
git config --global pull.rebase false
git config --global core.editor nano
```

Puis récupérez le projet :

```bash
git clone <URL-DONNÉE-PAR-L-ANIMATEUR>
cd dojo-git
```

---

## Niveau 1 : mon premier conflit

Deux branches existent déjà :

- `niveau1-exploitation` : passe l'app en HTTPS (port 443)
- `niveau1-dev` : met le port 3000 pour tester en local

1. Regardez l'historique de toutes les branches :
   ```bash
   git log --oneline --graph --all
   ```
2. Allez sur la branche de l'exploitation :
   ```bash
   git switch niveau1-exploitation
   ```
3. Fusionnez-y le travail du dev (`origin/` veut dire « la version sur GitHub ») :
   ```bash
   git merge origin/niveau1-dev
   ```
4. 💥 Conflit ! Regardez ce que Git dit :
   ```bash
   git status
   ```
5. Ouvrez `config.yml` (`nano config.yml`) et trouvez les marqueurs :
   ```
   <<<<<<< HEAD
   ce que VOUS avez (la branche où vous êtes)
   =======
   ce qui ARRIVE (la branche que vous fusionnez)
   >>>>>>> origin/niveau1-dev
   ```
   Choisissez le bon port, puis **supprimez les 3 lignes de marqueurs**.
6. Dites à Git que c'est réglé et terminez la fusion :
   ```bash
   git add config.yml
   git commit -m "Résout le conflit sur le port"
   ```
7. Admirez le résultat :
   ```bash
   git log --oneline --graph --all
   ```

❓ Le niveau de logs et le nom de la base de données ont aussi changé.
Pourquoi Git n'a-t-il pas fait de conflit pour eux ?

---

## Niveau 2 : deux personnes, un serveur

**Secret** : GitHub, c'est juste un autre repo Git, sur un autre ordinateur.
On va créer notre propre « faux GitHub » dans un dossier, puis deux personnes qui travaillent dessus :
**moi** et **collegue**.

```bash
git switch main
cd ..
git clone --bare dojo-git serveur.git
git clone serveur.git moi
git clone serveur.git collegue
```

Vous avez maintenant 3 dossiers : `serveur.git` (le faux GitHub), `moi` et `collegue`.
En binôme, l'un peut jouer **moi** et l'autre **collegue** sur le même ordinateur.

### 1. « moi » choisit une couleur et push

```bash
cd moi
nano equipe.md        # remplacez « à définir » par une couleur
git add equipe.md
git commit -m "Couleur du logo : bleu"
git push
```

### 2. « collegue » choisit une AUTRE couleur et push

```bash
cd ../collegue
nano equipe.md        # une couleur différente, sur la même ligne
git add equipe.md
git commit -m "Couleur du logo : rouge"
git push
```

❌ `rejected` ! Le serveur a un commit que « collegue » n'a pas encore.

### 3. « collegue » récupère et résout

```bash
git pull
```

💥 Conflit. Mettez-vous d'accord sur une couleur, enlevez les marqueurs, puis :

```bash
git add equipe.md
git commit -m "Résout le conflit sur la couleur"
git push
```

### 4. « moi » récupère la décision finale

```bash
cd ../moi
git pull
cat equipe.md
```

---

## Bonus (pour les rapides)

- Refaites un conflit avec « Nom de la mascotte », mais cette fois **dans l'autre sens** :
  « collegue » push en premier.
- Au lieu de résoudre, tapez `git merge --abort`. Que se passe-t-il ?
- `git diff` pendant un conflit montre les deux versions sans ouvrir le fichier.
- `git blame equipe.md` : qui a écrit quoi ?
