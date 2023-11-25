## Description

Lors de mon séjour en Grèce, j'ai été confronté à un problème mathématique que je nommais casse tête. Soyez rassuré ! Il était tellement facile que j’ai finalement choisi les maths au lycée . J'aimerais savoir si toi aussi tu réussiras à comprendre le même problème mathématique.

Ton objectif est de déchiffrer ce message :
`Ay4kCPEb4xgmXCBYYD/5kPFUYfU1zdJAucIwpNVQLQjeQ081R2uW9jy8qA==*8BFyo1Z3F3R/q+qTOISNzA==*G/sCnG7H/qfgJBHGXk6NbA==*bY08cXs8ZOb/b5mstzA5LA==`

Attention ! Tu peux le faire en python via la bibliothèque `rCJAE@4@56`. Mais NE SOIS PAS AUSSI NAÏF ! j’ai moi-même transformé cette bibliothèque en `rCJAE@4@56` en utilisant un premier chiffrement. Cependant, la bibliothèque commence par : `Cr........` Trouve les 8 lettres manquantes et déchiffre le texte pour obtenir ton flag.
NB : Pour le déchiffrement avec `python`, tu peux utiliser la clé : `FCC`
Format du flag: FCC{Message déchiffré}
Author : **Garlic**
## Solution
Pour resoudre ce challenge, j'avais besoin de decoder 2 chiffrement. Le 1er etant surement le `rCJAE@4@56`. ensuite le prochain qui es le plus important le message `Ay4kCPEb4xgmXCBYYD/5kPFUYfU1zdJAucIwpNVQLQjeQ081R2uW9jy8qA==*8BFyo1Z3F3R/q+qTOISNzA==*G/sCnG7H/qfgJBHGXk6NbA==*bY08cXs8ZOb/b5mstzA5LA==`

Mon outil favorite pour decoder des chiffrement inconnues est `dcode.fr`. Donc j'ai identifier que le `rCJAE@4@56` etait un `rot 47`. en le dechiffrant j'ai obtenue `Cryptocode`. 
Maintenant dans la description du challenge, on me fais comprendre que c'est un library python qui a ete utiliser pour le message encoder.
Donc avec peux de recherche j'ai ecris un script avec le bibliotheque `Cryptocode` pour le dechiffrer
```python
#!/usr/bin/python3

import cryptocode

message = "Ay4kCPEb4xgmXCBYYD/5kPFUYfU1zdJAucIwpNVQLQjeQ081R2uW9jy8qA==*8BFyo1Z3F3R/q+qTOISNzA==*G/sCnG7H/qfgJBHGXk6NbA==*bY08cXs8ZOb/b5mstzA5LA=="
key = "FCC"

flag = (cryptocode.decrypt(message, key))
print(flag)
```
Ensuite on obtient le Flag : `FCC-CTF{CrYpT0c0d!$C00L-!n-PyT0n_!LiK£_!T}`
### Ressources 
https://github.com/gdavid7/cryptocode
