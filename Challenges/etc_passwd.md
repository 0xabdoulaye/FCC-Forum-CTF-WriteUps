## Description

Un collaborateur de notre entreprise a été viré ce matin.
Dans son ordinateur se trouve des informations cruciales pour la présentation client de demain.
Sans ces informations, nous risquons de perdre notre plus gros client.
Nous avons réussi à recupérer son fichier `/etc/passwd` et un de ces collègues avec qui il travaille quotidiennement nous affirme que son mot de passe contient le mot `PuMa` sans préciser son nombre de caractères
Pouvez-vous nous aider à retrouver ce mot de passe?

Format du flag : FCC{....}

## Solution
Ce challenge baser sur le hash cracking, c'est sure que le challenge etait tres compliquer juste avec les outils de cracking. 
Ici la premiere chose que j'ai essayer d'abord c'est de generer un wordlist du mot `PuMa` avec `crunch`, mais rien n'a marcher.
So apres quelque minute de reflexion, j'ai decider de voir dans le wordlist `rockyou` pour voir toutes les mots qui contient le mot `PuMa`.
```terminal
┌──(root㉿1337)-[/home/…/CTFs/FCC_CTF/FCC-Forum-CTF-WriteUps/Challenges]
└─# cat /usr/share/wordlists/rockyou.txt | grep "PuMa"
PuMartin1
PuMaSuNaM31
PuMaS05
PuMaS
PuMa2009
```
Donc, j'ai seulement mis ces mots de passe dans une liste que j'ai appeler `wordlist.txt`,
Ensuite j'ai aussi mis mes hash dans un fichier `hash.txt`
```terminal
Amadimk:$y$uUSXwCvO$Ic9kN9dS0BHN.NU.5h7rAcEQbtjPjqWpej5o5y7JlrQK0hdQrzKBZB1V6CowHhCpk25PaieLcJEqC6e02ExYA.:18917
d34m0n:$y$iU9KjTeD$5myyo4W7zppTOEdVUeP8/E6Kmjl7CtYYFqIIyes.fnNHy1fR0gJLb0q2KLhjAH6KrPpHZ0eJorBh.D74mq.vQ.:17952
FCC_CONAKRY:$y$j9T$cqKy467Ni8MjREe2p8a120$OscchQk5oGGawo.AnTGwxDCxwnQCZmksjMgwcQPALd0
```
En lancant John
```terminal
└─# john hash.txt --wordlist=word.txt
Using default input encoding: UTF-8
Loaded 1 password hash (HMAC-SHA256 [password is key, SHA256 128/128 AVX 4x])
Will run 4 OpenMP threads
FCC_CONAKRY:PuMaSuNaM31

1 password hash cracked, 1 left
```
On obtiens le mot de passe : `PuMaSuNaM31`. 
Flag : `FCC{PuMaSuNaM31}`