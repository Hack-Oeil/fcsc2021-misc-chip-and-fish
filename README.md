# FCSC 2021 Chip and Fish

Nous avons mis en place un émulateur CHIP-8 en ligne. Il prend en entrée une chaîne hexadécimale représentant la ROM à exécuter. Serez-vous capable d’afficher le secret de 16 octets qui se trouve au début de la pile ?

Fichier : [emulator.zip](emulator.zip)

Notes :

- Note 1 : le flag attendu est de la forme ```FCSC{secret.hex().lower()}```.
- Note 2 : pour les utilisateurs sous Windows, voici un morceau de code permettant d’activer les caractères ANSI :
```python
"""
Enable Virtual Terminal Input
https://docs.microsoft.com/en-us/windows/console/setconsolemode
"""
import win32api
import ctypes

kernel32 = ctypes.windll.kernel32
hStdout = win32api.GetStdHandle(win32api.STD_OUTPUT_HANDLE)
result = kernel32.SetConsoleMode(hStdout, 0x1 | 0x2 | 0x4)
if result == 0:
    raise RuntimeError("Console doesn't support ANSI character")
```
La documentation associée à l’épreuve est disponible sur [cette page](DETAILS.md).


Auteur : sheidan

Origine : [Chip and Fish](https://hackropole.fr/fr/challenges/misc/fcsc2021-misc-chip-and-fish/)



## Connectez vous en WEBSSH
> http://localhost

#### tentez 
> nc chip-and-fish.cyrhades.com:4000


-----------

## Ou directement avec netcat
> nc localhost:4000

-----------


## Installation manuel
Vous n'utilisez pas l'application **les CTFs de Cyrhades** ? C'est dommage !
Mais voici comment installer ce CTF manuellement :

> git clone https://github.com/Hack-Oeil/fcsc2021-misc-chip-and-fish.git

> cd fcsc2021-misc-chip-and-fish


-----------

## Sur le site officiel hackropole.fr
> https://hackropole.fr/fr/challenges/misc/fcsc2021-misc-chip-and-fish/