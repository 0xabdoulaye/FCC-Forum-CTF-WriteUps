## Casse tete 2
Decoder le Message de La Chine.
```
簷簷簶 簷簶簷 簶簷簶簷 簶簶簷簷 簷簶簶簷
簷 簶簶簷簶 簶 簶簷簶簶 簷簶簷簷
簶簶簷簷 簷簷簶 簷簷 簷簶 簶簷簷簷
簷簷 簷簷簷簷 簷簶簷簷 簷簶簶 簶簶簶
簶簷簷簷 簶簶簶 簶簷簶簶
```

## Solution
Ce Message est un `rot8000`, je l'ai donc decoder avec l'outil Ultime `Cyberchef` pour avoir du Morse Code. 
```terminal
..- .-. -.-. --.. .--.
. --.- - -.-- .-..
--.. ..- .. .- -...
.. .... .-.. .-- ---
-... --- -.--
```
Donc j'ai decoder aussi ce Morse Code pour avoir un chiffrement de `Rot13` se terminant par Boy
`URCZP EQTYL ZUIAB IHLWO BOY`
Ensuite en dechiffrant ceci j'ai pu avoir : `HEPMC RDGLY MHVNO VUYJB OBL`
Apres tant de recherche sur internet, au debut j'ai utiliser `enigma` sur `Cryptii` mais ca n'a rien Donner. Donc je l'ai essayer avec `enigma` aussi sur `CyberChef` et ca a donner le Flag.
`ILSON TFLAG TOUSN OSCHA LLS` == `ILS ONT FLAG TOUS NOS CHALLS`
`FCC{ILS ONT FLAG TOUS NOS CHALLS}`