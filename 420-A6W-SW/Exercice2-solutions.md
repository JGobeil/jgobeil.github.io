---
layout: page
title:  "420-A6W-SW/Exercices2"
date:   2014-11-11 23:29:00
categories: A6W, Exercices, Algorithme
---

#420-A6W-SW : Exercices 2 (avec solutions)

## Main

Nous allons commencer par écrire une fonction `Main`. Celle-ci sera le point d'entrée de notre jeu. Lisez la fonction et tâchez de comprendre les différentes commandes qui sont exécutées. Les commentaires devraient vous aider à comprendre.

Plusieurs structures et fonctions seront définies par la suite. Écrire ce genre d'algorithme permet de prévoir quel genre de structure et de fonction seront nécessaire.

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

   P

   // Creation du joueur
   Joueur joueur <- CreationJoueur(0, 0, 3, armes[0], armures[0])

   AfficherMonde(carte, joueur, ennemis)
   AfficherMessage("Bienvenu dans le jeu " + nom_du_jeu + ".")
   AfficherMessage("Touche 'h' pour de l'aide.")

   // L'action à effectuer
   String action <- "Ne pas quitter"

   TantQue action != "Quitter" Faire

      touche <- LireClavier()

      // Obtenir l action correspondant à la touche
      action <- ActionDeLaTouche(touche)

      // Effectuer l action en conséquence
      Si action == "deplacement" Alors
         // Obtenir la direction du déplacement
         Pos deplacement <- DeplacementDeLaTouche(touche)

         // Calculer la nouvelle position
         Position nPos <- AdditionPosition(joueur.pos, deplacement)

         // Le type de terrain de la nouvelle position
         // Par défaut considéré hors de la carte
         String tile <- "PasDansLaCarte"

         // Vérifiez que la nouvelle position est à l'intérieur de la carte
         Si EstDansLaCarte(carte, nouvellePos) Alors
            Booleen deplacer = Vrai

            // Obtenir l element de carte à cet endroit
            tile <- carte.tiles[nPos.x, nPos.y]

            // Faire une condition pour chaque cas possible
            // Un même 'tile' peut avoir plusieurs conséquences?

            // Vérifier si cible correspond à un obstacle
            Si EstUnObstacle(tile) Alors
               // Indiquer au joueur qu il ne peut aller a cet endroit
               MessageObstacle(tile)
               deplacer = Faux
            FinSi

            // Chercher si un ennemi est présent à cet endroit
            int ei = TrouverEnnemi(ennemis, nouvellePos)

            // Attaquer si un ennemi s'y trouve
            Si  ei >= 0 Alors
               // Attaquer
               AttaquerEnnemi(joueur, ennemis[e])
               deplacer = Faux
            FinSi

            // Deplacer le joueur
            Si deplacer == Vrai Alors
               joueur.pos <- nPos
            FinSi
         FinSi
      FinSi
   FinTantQue
FinFonction
```

## La carte

On va représenter la carte par un tableau 2D où chaque entrée correspondra à une position sur la carte. Les entrées seront de type `String`, ce qui nous permet de représenter divers éléments simplement. Par exemple, un éléments de sol standard sera représenté par le `String` "sol", un arbre par "arbre", une roche par "roche", etc.

### Définition de la structure
Nous allons définir une structure afin de contenir les informations pertinentes reliées à la carte, comme sa largeur, sa hauteur et ses _tiles_.

```C
// La carte du monde
Structure Carte:
   int dim_x // La dimension en x de la carte
   int dim_y // La dimension en y de la carte
   String[,] tiles // Un tableau 2D de String
   Ennemi[] ennemis // Listes des ennemis
FinStructure
```

#### Exemple
À titre d'exemple, une structure Carte de dimension 4x5, avec un mur qui
va de la position [2,2] à la position [4,2] et un arbre à la
position [2,0] peut être représenté schématiquement comme:

```
int dim_x = 4
int dim_y = 5
String[,] tiles = +-------+-------+-------+-------+
                  | "sol" | "sol" | "sol" | "sol" | [0, ]
                  +-------+-------+-------+-------+
                  | "sol" | "sol" | "sol" | "sol" | [1, ]
                  +-------+-------+-------+-------+
                  |"arbre"| "sol" | "mur" | "sol" | [2, ]
                  +-------+-------+-------+-------+
                  | "sol" | "sol" | "mur" | "sol" | [3, ]
                  +-------+-------+-------+-------+
                  | "sol" | "sol" | "mur" | "sol" | [4, ]
                  +-------+-------+-------+-------+
                    [ ,0]   [ ,1]   [ ,2]   [ ,3]
```

### Fonction de création d'une carte de dimension 20 x 30
Nous allons maintenant définir une fonction permettant de créer une carte
standard de dimension 20 x 30. Il serait possible de créer d'autre fonction
de création afin de créer des cartes différentes, ou même de créer des cartes
aléatoirement.

Pour l'instant contentons d'une simple carte prédéfinie.

```C
// Creation d une nouvelle carte
// Parametres:
//      Ennemi[] ennemis: Liste des ennemis
// Retourne: Carte
// 	Retourne une nouvelle carte de dimension 20 x 30
Fonction CreationCarte20x30(Ennemi[] ennemis): Carte
   // Déclaration d une variable de type c
   Carte c

   // Assigner 20 à la dimension en x de la carte
   c.dim_x <- 20

   // Assigner 30 à la dimension en y de la carte
   c.dim_y <- 30

   // Création d un tableau de String de dimension [x,y]
   c.tiles <- String[c.dim_x, c.dim_y]

   // Mettre dans chaque entrée du tableau, la valeur "sol"
   Pour i de 0 a c.dim_x-1 Faire
      Pour j de 0 a c.dim_y-1 Faire
         c.tiles[i, j] <- "sol"
      FinPour
   FinPour

   // Ajouter d'autres éléments
   DessinerCarte20x30(c)

   // Ajouter les ennemis
   AjouterEnnemis20x30(c, ennemis)

   // Retourner la nouvelle carte
   Retourne c
FinFonction
```

Nous allons utiliser la procédure `DessinerCarte20x30` pour ajouter des éléments autre que "sol" à notre carte.

_Il serait intéressant de créer une fonction qui permettrait de lire une carte dans un fichier.

### Exercice: Définir la procédure `DessinerCarte20x30`
Dessinez une petite carte de dimension 20x30. Vous pouvez y mettre des murs, des roches, ou tout autres éléments de décors que vous trouvez intéressant.

Écrivez la procédure `DessinerCarte20x30(Carte crt)`, qui va permettre d'initialiser cette carte dans le jeu. Donner un nom, sous la forme d'un `String`, aux différents éléments de la carte ("mur", "roche", ...).

```C
// Dessiner la carte 20x30
// Paramètres:
// 	Carte crt: La carte à ajouter des éléments
Procedure DessinerCarte20x30(Carte crt)
   crt[3, 3] <- "Arbre"
   Pour i de 5 a 10 Faire
      crt[i, 7] <- "Mur"
   FinPour
FinProcedure
```

### Exercice: Procedure `AjouterEnnemis20x30`
Créer une procédure qui permet de rajouter les ennemis dans la carte.

1. Initialiser la liste des ennemis de la carte ( 6 ennemis)
2. Ajouter les ennemis ( ennemis de niveau 1 et 2 seulement)
3. Postionner de façon aléatoire dans la carte les ennemis. Ne pas les positionner sur des obstacles.

## La structure `Position`

Étant donnée que nous allons travailler régulièrement avec un binôme x et y, il serait pratique de créer une structure et quelques fonctions afin de faciliter leurs utilisation.

### Exercice: Définir la structure `Position`
Définir une structure qui permet de représenter une position sur la carte. Une position peut-être représentée par deux `int`, une pour la valeur en x et l'autre pour la valeur en y.

```C
Structure Position
   int x
   int y
FinStructure
```

### Exercice: Définir une fonction de création
Définir une fonction pour créer et initialiser facilement une nouvelle `Position`.

```C
// Créer et initialiser une nouvelle position
// Paramètres:
//      int x: valeur en x de la nouvelle position
//      int y: valeur en y de la nouvelle position
// Retourne: Position
//      La nouvelle position
Fonction CreerPosition(int x, int y): Position
   // Creer une nouvelle variable de type Position
   Position p

   // Initialiser les valeurs de la position
   p.x <- x
   p.y <- y

   // Retourner la nouvelle position
   Retourne p
FinFonction
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
Définir une fonction qui permet d'addition deux positions. Doit retourner une nouvelle `Position`.

```C
// Addition de deux positions
// Paramètres:
//      Position p1: Premier position
//      Position p2: Deuxième position
// Retourne: Position
//      Nouvelle position qui est la somme des positions p1 et p2
Fonction AdditionPos(Position p1, Position p2): Position
   Position p
   p.x <- p1.x + p2.x
   p.y <- p1.y + p2.y
   Retourne p
FinFonction
```

Ou en utilisant la fonction `CreerPosition`

```C
// Addition de deux positions
// Paramètres:
//      Position p1: Premier position
//      Position p2: Deuxième position
// Retourne: Position
//      Nouvelle position qui est la somme des positions p1 et p2
Fonction AdditionPos(Position p1, Position p2): Position
   Retourne CreerPosition(p1.x + p2.x, p1.y + p2.y)
FinFonction
```

#### Exercice: Fonction de différence entre deux positions
Définir une fonction qui permet de faire la différence entre deux positions. Doit retourner une nouvelle `Position`.

```C
// <Description>
// Paramètres:
//      Position p2: Première position
//      Position p2: Deuxième position
// Retourne: Position
//      Une position qui est la différence entre p1 et p2
Fonction DifferencePos(Position p1): Position
   Retourne CreerPosition(p1.x - p2.x, p1.y - p2.y)
FinFonction
```

### Fonctions d'interaction `Position` -- `Carte`

#### Exercice: Fonction `EstDansLaCarte(Carte c, Position p)`
Écrire une fonction qui permet de vérifier si la `Position p` se trouve dans les limites de la `Carte c`. Retourne _vrai_ ou _faux_ (`true` ou `false` si vous préférez).

```C
// Vérifier si la position est dans la carte
// Paramètres:
//      Carte c: La carte
//      Position p: La position à vérifier
// Retourne: Booleen
//      Vrai si p est dans la carte, sinon retourne faux
Fonction EstDansLaCarte(Carte c, Position p): Booleen
   Si p.x < 0 ou p.x >= c.dim_x Alors
      Retourne Faux
   Sinon Si p.y < 0 ou p.y >= c.dim_y Alors
      Retourne Faux
   Sinon
      Retourne Vrai
   FinSi
FinFonction
```

#### Exercice: Fonction `EstUnObstacle(Carte c, Position p)`
Écrire une fonction qui permet de vérifier si la `Position p` est un obstacle (une "roche" par exemple). Retourne _vrai_ ou _faux_ (`true` ou `false` si vous préférez).

```C
// Vérifier si la position est un obstacle
// Paramètres:
//      Carte c: La carte
//      Position p: La position à vérifier
// Retourne: Booleen
//      Vrai si p est un obstacle, sinon faux
Fonction EstUnObstacle(Carte c, Position p): Booleen
   // Aller chercher le type de terrain
   String t <- c.tiles[p.x, p.y]
   Si t == "arbre" Alors
      Retourne Vrai
   Sinon Si t == "mur" Alors
      Retourne Vrai
   Sinon
      Retourne Faux
FinFonction
```

### La structure `Arme`
Définissons maintenant des structures et fonctions pour la gestion des armes. Une arme aura un nom et valeur d'attaque.

### Définition de la structure

```C
Structure Arme:
   String nom
   int attaque
FinStructure
```

### Fonction de création d'une liste d'armes

Afin de facilité la création des armes, nous allons créer une fonction qui créera la liste des armes qui existe dans le jeu. Il sera ainsi possible de se référer à celle-ci pour attribuer les armes aux joueurs.

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
Définissons maintenant des structures et fonctions pour la gestion des armures. Une armure aura un nom et valeur de défense.

### Exercice: Définir la structure
Définir une structure pour les armures.

```C
Structure Armure:
   String nom
   int defense
FinStructure
```

### Exercice: Définir une fonction de création d'une liste d'armures
Définir la fonction `ListeDesArmures()` qui permet d'obtenir la liste des armures du jeu. La fonction doit retourner une liste de structure `Armure`.

```C
// Création de la liste des armures
// Retourne: Armures[]
// 	Une liste d armures
Fonction ListeDesArmures(): Armures[]
   // Creation d'une liste d'armures
   Armure[4] a

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

## La structure `Joueur`

### Exercice: Définir la structure `Joueur`
Définir une structure qui permet de représenter un joueur.
Un joueur doit avoir comme attributs:

* Une position (_indice: Vous avez défini une structure `Position`_)
* Un nombre de point de vie actuel
* Un nombre de point de vie maximal
* Une arme de type `Arme`
* Une armure de type `Armure`

```C
Structure Joueur
   Position pos
   int pv
   int pv_max
   Arme arme
   Armure armure
FinStructure
```

### Exercice: Fonction de création d'un `Joueur`
Définir une fonction pour créer et initialiser un nouveau joueur

```C
// Creation et initialisation d un nouveau joueur
// Paramètres:
//      int x: position en x
//      int y: position en y
//      int pv: Nombre de point de vie initial (et maximum)
//      Arme arme: Arme initiale
//      Armure armure: Armure initiale
// Retourne: Joueur
// 	La nouvelle variable position
Fonction CreationJoueur(int x, int y, int pv, Arme arme, Armure armu): Joueur
   Joueur j
   j.pos <- CreerPosition(x, y)
   j.pv <- pv
   j.pv_max <- pv
   j.arme <- arme
   j.armure <- armu
   Retourne j
FinFonction
```

## Les ennemis

### La structure `Ennemi`
La structure ennemi doit avoir les informations suivantes:

* Un nom
* Un nombre de point d'attaque
* Un nombre de point de défense
* Un nombre de point de vie
* Une `Position`

```C
Structure Ennemi
   String nom
   Position pos
   int pv
   int attaque
   int defense
FinStructure
```

### Fonction `ListeDesEnnemis()`
Écrire la fonction `ListeDesEnnemis20x30()` qui permet de créer et de placer les ennemis.

```C
// Creer la liste des ennemis
// Retourne: Ennemi[]
//      La liste des ennemis
Fonction ListeEnnemis(): Ennemi[]
   // Creer le tableau des ennemis
   Ennemis[4] e

   e[0].nom = "Slime"
   int pv = 2
   int attaque = 2
   int defense = 0

   e[1].nom = "Kobolt"
   e[1].pv = 4
   int attaque = 3
   int defense = 2

   e[2].nom = "Gobelin"
   e[2].pv = 7
   int attaque = 6
   int defense = 4

   e[3].nom = "Dragon"
   e[3].pv = 1500
   int attaque = 300
   int defense = 1000

   Retourne e
FinFonction
```


### Exercice: Fonction `TrouverEnnemi(Ennemi[] e, Position p)`
```C
// Chercher si un ennemi est à la position p
// Paramètres:
//      Ennemi[] e: Liste des ennemis
//      Position p: Position où chercher s'il y a un ennemi
// Retourne: int
//      L'emplacement dans la liste ennemi de l'énnemi qui est à la
//      position p. Retourne -1 s'il n'y a pas d'ennemi à la postion p
Fonction TrouverEnnemi(Ennemi[] e, Position p): int
```

### Exercice: Fonction `AttaquerEnnemi(Joueur j, Ennemi e)`
Définir des régles de combat et les implémenters. L'effet de l'attaque et de la défense? Une attaque par tour, ou jusqu'à ce mort s'en suive? Le joueur se déplace si il gagne le combat? ....


## Fonctions prédéfinies
Nous allons considérer quelques fonctions comme existantes. Il s'agit plus de question d'implémentation techniques et sont largement dépendantes de la plateformes et du langage utilisées.

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

### Exercice: Liste aléatoire
Écrire une fonction qui permet de faire un choix aléatoire dans une liste.

### Exercice (compliqué): Liste aléatoire avec poids
Écrire une fonction qui permet de faire un choix aléatoire dans une liste avec poids, c'est-à-dire que chaque éléments de la liste est accompagnés d'un poids et que plus le poids est élevé, plus l'élément à des chances d'être choisi.
