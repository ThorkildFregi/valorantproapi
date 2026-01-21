# API

## Example

Pour pouvoir importer le package :

```python
import sys

sys.path.insert(0, "path to /src/")
```

Puis les imports :

```python
from api.raspConnection import Connection, create_ports
```

--------------------------

Pour créer des ports virtuels, il suffit de créer une variable qui appelle [create_ports](#create_ports-(Fonction)) avec en argument deux noms de ports sous la forme ```/tmp/ttyVX``` où ``X`` est un nombre.

```python
proc = create_ports("/tmp/ttyV0", "/tmp/ttyV1")
```

REMARQUE : Ne pas oubliez de tuer le process à la fin du code

```python
proc.kill()
```

--------------------------

Une fois les ports créés, on peut créer un object [Connection](#connection-(class)) en mettant l'un de nos ports en argument (l'autre servira pour un autre objet Connection). Une fois l'objet créé on le lance avec la fonction [start()](#connection.start-(fonction)).

```python
conn = Connection("/tmp/ttyV0")

conn.start()
```

--------------------------

Pour envoyer des données, il suffit d'utiliser la fonction [send()](#connection.send()-(fonction)).

```python
conn.send("exemple", "ceci n'est pas un message")
```
Le premier argument est l'ordre et le second les data que l'ont cherche à envoyer.


**REMARQUE** : On peut aussi envoyer des messages sans ordre

```python
conn.sendRaw("exemple", "ceci n'est pas un message")
```

--------------------------

Pour recevoir les messages, il faut créer un handler.

```python
@conn.on("exemple")
def handler(data):
    print(data)
```
Ici à chaque message reçu avec l'ordre ```exemple```, les data seront print sur le terminal.

**REMARQUE** : On peut aussi recevoir n'importe quelle message

```python
@conn.onMessage()
def handler(data):
    print(data)
```

Ou que les messages sans ordre

```python
@conn.onRaw()
def handler(data):
    print(data)
```

--------------------------

Pour arrêter la connection, il faut utiliser la fonction [stop()](#connection.stop()-(fonction)).

```python
conn.stop(0)
```

Le premier argument est le code de sortie. ```0``` si le code a bien marché, sinon ```9```

--------------------------

### Example complet

```python
import sys

sys.path.insert(0, "path to /src/")

from api.raspConnection import Connection, create_ports

proc = create_ports("/tmp/ttyV0", "/tmp/ttyV1")

conn1 = Connection("/tmp/ttyV0")
conn2 = Connection("/tmp/ttyV1")

@conn.on("exemple")
def handler(data):
    print(data)

conn1.start()
conn2.start()

conn1.send("exemple", "ceci n'est pas un message")

conn1.stop(0)
conn2.stop(0)

proc.kill()
```

## Fonctions et Classes

### create_ports (fonction)

Crée des ports à l'aide d'une commande socat.

***arguments :***
- **port1** : Premier nom de port utilisé par socat dans la création des ports
- **port2** : Second nom de port utilisé par socat dans la création des ports

**REMARQUE** : Doit-être de la forme ```/tmp/ttyVX``` où ``X`` est un nombre.
