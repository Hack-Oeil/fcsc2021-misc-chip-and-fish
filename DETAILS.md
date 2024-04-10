## Contexte
L’entreprise Chip&Fish a mis au point une nouvelle architecture sécurisée baptisée BattleChip. Elle a implémenté une machine virtuelle légère (CHIP-8) ainsi qu’un élément sécurisé où une clé secrète est générée aléatoirement à chaque exécution du système.

## Description du produit
Au-delà des caractéristiques originales (voir https://en.wikipedia.org/wiki/CHIP-8#Virtual_machine_description), un élément sécurisé a été ajouté :

![VM.png](VM.png)

Schéma de fonctionnement du produit

L’élément sécurisé n’est rattaché à aucun périphérique. Il contient du code pré-compilé dans lequel vient s’ajouter la clé secrète précédemment générée.

Les opérations se déroulent presque toutes en 1 cycle sauf pour les opérations qui nécessitent l’utilisation de l’unité arithmétique et logique (UAL) auquel cas, 2 cycles seront nécessaires : un cycle pour soumettre le calcul à l’UAL, un cycle pour récupérer le résultat. Pour palier à ce problème de performance, le constructeur a mis au point un système de cache partagé entre la VM utilisateur et l’élément sécurisé. Le cache repose sur un algorithme de type LRU et est placé en amont de l’UAL. Ainsi, si une opération a récemment été réalisée, le CPU a directement connaissance du résultat coûtant donc qu’un seul cycle.

En lien avec la gestion du cache, le constructeur a ajouté le code opération 00E1. Cette nouvelle instruction permet de vider toutes les lignes du cache de l’UAL.

Le constructeur a également implémenté deux autres codes opération : 0000 et 0001. Voici respectivement le pseudocode de chacune des routines :
```python
def encrypt_0000(I):
    """
    Xor a buffer stores at I with the secret key.
    The secret key is already defined.

    I is copied from the insecure context

    =>  The number of cycles needed to execute this routine
        will be copied at the end of execution in the insecure context.
        The 0xF-th register will contain the value.
    """
    secure_memory[I] ^= k0
    I += 1
    secure_memory[I] ^= k1
    I += 1
    secure_memory[I] ^= k2
    I += 1
    secure_memory[I] ^= k3
    I += 1
    secure_memory[I] ^= k4
    I += 1
    secure_memory[I] ^= k5
    I += 1
    secure_memory[I] ^= k6
    I += 1
    secure_memory[I] ^= k7
    I += 1
    secure_memory[I] ^= k8
    I += 1
    secure_memory[I] ^= k9
def verify_0001(I):
    """
    This routine checks if the buffer matches the secret key.
    The secret key is already defined.

    I is copied from the insecure context

    =>  0xF-th register in the secure context will be copied
        in the insecure context at the end of this routine execution.
    => if vF is zero, the flag is set at I+10 in the unsecure memory.
        The flag's size is 16 bytes. We expected FCSC{<hex_encoded_flag>} on
        the platform.
    """
    vF = 0
    vF |= secure_memory[I] ^ k0
    vF |= secure_memory[I] ^ k1
    vF |= secure_memory[I] ^ k2
    vF |= secure_memory[I] ^ k3
    vF |= secure_memory[I] ^ k4
    vF |= secure_memory[I] ^ k5
    vF |= secure_memory[I] ^ k6
    vF |= secure_memory[I] ^ k7
    vF |= secure_memory[I] ^ k8
    vF |= secure_memory[I] ^ k9
```

Ces deux actions sont exécutées par l’élément sécurisé.

Le constructeur a également laissé quelques notes sur les périphériques :
```
--- Clavier ---

Pas de clavier, pas de pong. Pas de pong... pas de pong.
Utiliser un code d'opération nécessitant une intéraction
le client terminera l'execution du programme.

--- Horloge ---

Rien à déclarer capitaine.

--- Sirène ---

Pour le bonheur de vos oreilles, la sirène ne fait aucun bruit.
Elle est toute fois connectée.

--- Ecran ---

L'écran envoie des caractères ANSI pour contrôler l'affichage.
Il est recommandé d'avoir une console de taille 64x32 ou
supérieure. La VM ne renvoie pas d'informations spécifique
sauf s'il y a une erreur lors de l'exécution.
Faites donc bonne usage de l'écran.
```

Pour les utilisateurs sous Windows, voici un exemple de code pour activer le support des caractères ANSI :

```python
# Windows user ONLY
import os
import win32api
import ctypes

def activate_vti():
    """Enable Virtual Terminal Input

    Documentation: https://docs.microsoft.com/en-us/windows/console/setconsolemode
    """
    if os.name == "nt":
        kernel32 = ctypes.windll.kernel32
        hStdout = win32api.GetStdHandle(win32api.STD_OUTPUT_HANDLE)
        result = kernel32.SetConsoleMode(hStdout, 0x1 | 0x2 | 0x4)
        if result == 0:
            raise RuntimeError("Console doesn't support ANSI character")
```