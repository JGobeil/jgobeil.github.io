#420-A6W-SW : Exercices 2

## Main

```C
// Fonction principale du jeu
Fonction Main():
   String nom_du_jeu <- "Super Algo Rpg"

   // Creation de la carte du monde
   Carte carte <- CreationCarte20x30()

   // Creation des armes, armures et ennemis
   Arme[] armes <- ListeDesArmes()
   Armure[] armures <- ListeDesArmures()
   Ennemi[] ennemis <- ListeDesEnnemis()

   // Creation du joueur
   Joueur joueur <- CreationJoueur(0, 0, 3, armes[0], armures[0])

   AfficherMonde(carte, joueur, ennemis)
   AfficherMessage("Bienvenu dans le jeu " + nom_du_jeu + ". Touche 'h' pour de l'aide.")

   // L'action à effectuer
   String action <- "Ne pas quitter"

   TantQue action != "quitter" Faire

      // Lire le clavier et mettre le retour dans la variable touche
      touche <- LireClavier()

      // Obtenir l action correspondant à la touche
      action <- ActionDeLaTouche(touche)

      // Effectuer l action en conséquence
      Si action == "deplacement" Alors
         // Obtenir la direction du déplacement
         Pos deplacement <- DeplacmentDeLaTouche(touche)

         // Calculer la nouvelle position
         Position nPos <- AdditionPosition(joueur.pos, deplacement) 

         // La tile cible (par défaut considéré extérieur)
         String tile <- "PasDansLaCarte"

         // Vérifiez que la nouvelle position est à l'intérieur de la carte
         Si DansLaCarte(carte, nouvellePos) Alors
            // Obtenir l element de carte à cet endroit
            tile <- carte.tiles[nPos.x, nPos.y]
         FinSi
         
         // Vérifier si la nouvelle position correspond à un obstacle
         Si EstUnObstacle(tile) Alors
            MessageObstacle(tile)

         Sinon Si EstUnEnnemis(ennemis, nouvellePos) Alors
            // Attaquer 
            AttaquerEnnemi(joueur, ennemis, nouvellePos)

         FinSi
      FinSi
   FinTantQue
FinFonction
```

## La carte

### Définition de la structure
La carte est décrite par un tableau en deux dimension.

```C
// La carte du monde
Structure Carte:
   int dim_x // La dimension en x de la carte
   int dim_y // La dimension en y de la carte
   String[,] tiles // Un tableau 2D de String.
FinStructure
```

### Fonction de création d'une carte de dimension 20 x 30
```C
// Creation d une nouvelle carte
// Retourne: Carte
// 	Retourne une nouvelle carte de dimension 20 x 30
Fonction CreationCarte20x30(int x, int y): Carte
   // Déclaration d une variable de type c
   Carte c 
   
   // Assigner 20 à la dimension en x de la carte
   c.dim_x <- 20

   // Assigner 30 à la dimension en y de la carte
   c.dim_y <- 30

   // Création d un tableau de String de dimension [x,y]
   c.tiles <- String[x, y]

   // Mettre dans chaque entrée du tableau, la valeur "sol"
   Pour i de 0 à c.dim_x-1 Faire
      Pour j de 0 à c.dim_y-1 Faire
         c.tiles[i, j] <- "sol"
      FinPour
   FinPour

   // Ajouter des obstaces
   DessinerCarte20x30(c)

   // Retourner la nouvelle carte
   Retourne c
FinFonction
``` 

### Exercice: Définir la procédure `DessinerCarte20x30`
Dessiner une petite carte de dimension 20x30. Vous pouvez y mettre des murs, des roches, ou tout autres éléments de décors que vous trouvez intéressant. Cette carte sera la première carte du jeu.

Une fois la carte dessinée, écrire la procédure `DessinerCarte20x30(Carte crt)`, qui va permettre d'initialiser cette carte dans le jeu. Donner un nom, sous la forme d'un `String`, aux différents éléments de la carte ("mur", "roche", ...).

```C
// Dessiner la carte 20x30
// Paramètres:
// 	Carte crt: La carte à ajouter des éléments
Procedure DessinerCarte20x30(Carte crt)
```


## La structure `Position`

### Exercice: Définir la structure `Position`
Définir une structure qui permet de représenter une position sur la carte. Une position peut-être représentée par deux `int`, une pour la valeur en x et l'autre pour la valeur en y.

### Exercice: Définir une fonction de création
Définir une fonction pour créer et initialiser facilement une nouvelle `Position`. 

#### Prototype de la fonction
```C
// Créer et initialiser une nouvelle position
// Paramètres:
// 	int x: Valeur en x
//      int y: Valeur en y
// Retourne: Position
// 	La nouvelle variable position
Fonction CreerPosition(int x, int y): Position
```

### Fonctions de manipulation
Nous allons maintenant définir quelques fonction qui nous permettrons de manipuler facilement la structure `Position`.

#### Fonction d'ajout
```C
// Ajouter une valeur en x et en y à une position.
// Paramètres:
//      Position p: Position original
//      int x: valeur à ajouter en x
//      int y: valeur à ajouter en y
// Retourne: Position
//      Retourne la nouvelle position  
Fonction PosPlusXy(Position p, int x, int y): Position
   Retourne CreerPosition(p.x + x, p.y + y)
FinFonction
```

#### Exercice: Fonction d'addition de deux positions
Définir une fonction qui permet d'addition deux positions. Doit retourner une nouvelle `Position`

##### Prototype de la fonction
```C
// Addition de deux positions
// Paramètres:
//      Position p1: Premier position
//      Position p2: Deuxième position
// Retourne: Position
//      Nouvelle position qui est la somme des positions p1 et p2
Fonction AdditionPos(Position p1, Position p2): Position
```

#### Exercice: Fonction de différence entre deux positions
Définir une fonction qui permet de faire la différence entre deux positions. Doit retourner une nouvelle `Position`.


### La structure `Arme`

### Définition de la structure

```C
Structure Arme:
   String nom
   int attaque
FinStructure
```

### Fonction de création d'une liste d'armes

```C
// Création de la liste des armes
// Retourne: Arme[]
// 	Une liste d armes
Fonction ListeDesArmes(): Arme[]
   // Creation d'une liste d'armes
   Arme[4] a

   // Arme 1 
   a[0].nom <- "Tulipe"
   a[0].attaque <- 1

   // Arme 2
   a[1].nom <- "Rose"
   a[1].attaque <- 3

   // Arme 3
   a[2].nom <- "Couteau à beurre"
   a[2].attaque <- 5

   // Arme 4
   a[3].nom <- "Chaudron"
   a[3].attaque <- 9

   // Retourner la liste
   Retourne a
FinFonction
```

## La structure `Armure`

### Exercice: Définir la structure
Définir une structure pour les armures. Une armure doit avoir un nom et une valeur en défense.

<!--
```C
Structure Armure:
   String nom
   int defense
FinStructure
```
-->

### Exercice: Définir une fonction de création d'une liste d'armures
Définir la fonction `ListeDesArmures()` qui permet d'obtenir la liste des armures du jeu. La fonction doit retourner une liste de structure `Armure`. 

<!--
```C
// Création de la liste des armures
// Retourne: Armures[]
// 	Une liste d armures
Fonction ListeDesArmures(): Armures[]
   // Creation d'une liste d'armures
   Armues[4] a

   // Armure 1 
   a[0].nom <- "T-shirt de Spider-man"
   a[0].defense <- 1

   // Armure 2
   a[1].nom <- "Chandail de noël"
   a[1].defense <- 2

   // Armure 3
   a[2].nom <- "Manteau en cuir"
   a[2].defense <- 4

   // Arme 4
   a[3].nom <- "Boîte en carton"
   a[3].defense <- 12

   // Retourner la liste
   Retourne a
FinFonction
```
-->

## La structure `Joueur`

### Exercice: Définir la structure `Joueur`
Définir une structure qui permet de représenter un joueur. 
Un joueur doit avoir comme attributs:
* Une position (_hint: Vous avez défini une structure `Position`_)
* Un nombre de point de vie actuel
* Un nombre de point de vie maximal
* Une arme de type `Arme`
* Une armure de type `Armure`

### Exercice: Fonction de création d'un `Joueur`
Définir une fonction pour créer et initialiser un nouveau joueur 

```C
// Creation et initialisation d un nouveau joueur
// Paramètres:
// 	Position pos: Position initale
//      int pv: Nombre de point de vie initial (et maximum)
//      Arme arme: Arme initiale
//      Armure armure: Armure initiale
// Retourne: Joueur
// 	La nouvelle variable position
Fonction CreationJoueur(Position pos, int pv, Arme arme, Armure armu): Joueur
```

## Fonction que nous allons considérer comme existantes et fonctionnelles.

### Affichage

```C
// Afficher le monde, le joueurs et les ennemis à l écran
// Paramètres:
//      Map m: La carte du monde
//      Joueur j: Le joueur
//      Ennemis[] e: La liste des énnemis
Procedure AfficherMonde(Map m, Joueur j, Ennemis[] e)
```

```C
// Affiche un message au joueur
// Paramètres:
// 	String message: Message à afficher
Procedure AfficherMessage(String message)
```

```C
// Obtenir un nombre aléatoire
// Paramètres:
// 	int a: valeur minimale
//      int b: valeur maximale
// Retourne: int
// 	Retourne une valeur aléatoire entre a et b inclusivement
Fonction Random(int a, int b): int
```

Exercice : Écrire une fonction qui permet de faire un choix aléatoire dans une liste. 

Exercice++ : Écrire une fonction qui permet de faire un choix aléatoire dans une liste avec poids.
