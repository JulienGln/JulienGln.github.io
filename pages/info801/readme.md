# TP1 INFO801
### COMBE Théo et GALERNE Julien

## Implémentation en Linda du système de drainage dans une mine
L'objectif de ce TP est d'implémenter le sytème de drainage dans une mine, vu en TD. Ce sytème doit pouvoir pomper l'eau dans une galerie dès lors qu'elle dépasse un certain seuil tout en contrôlant les niveaux de deux gaz CH4 et CO. En effet, si ces deux gaz dépasse un certain seuil, la pompe ne doit pas être en marche et un ventilateur doit s'activer pour dissiper les gaz.

threads + espace de tuple

### Implémentation de Linda
Après avoir cherché sur Internet les possibilités d'implémentation de Linda dans différents langages, nous avons finalement décidé de partir sur notre propre implémentation de Linda en Python (la librairie existante étant trop ancienne).

Nous avons donc crée une classe ```TupleSpace``` pour représenter l'espace de tuples de l'architecture à mémoire partagée, conçue selon les principes de Linda.
Les opérations sur cet espace de tuples sont aussi présentes dans l'implémentation suivante :


```py
from threading import Lock

class TupleSpace:
    def __init__(self):
        self.tuples: list[tuple] = []
        self.lock = Lock()

    def out(self, tuple_data):
        """Place un tuple dans l'espace tuples"""
        self.tuples.append(tuple_data)

    def In(self, template):
        """Extrait un tuple de l'espace tuples correspondant à un motif donné (s'il n'y a pas de correspondance, 
        l'opération est bloquée)."""
        while True:
            with self.lock:
                for tpl in self.tuples:
                    if self.match(template, tpl):
                        self.tuples.remove(tpl)
                        return tpl

    def inp(self, template):
        """Une version non bloquante de in (s'il n'y a pas de correspondance, un message d'erreur est renvoyé)."""
        for i, tpl in enumerate(self.tuples):
            if self.match(template, tpl):
                return self.tuples.pop(i)
        return None
        
    def rd(self, template):
        """Copie un tuple qui correspond à un motif donné de l'espace tuples (s'il n'y a pas de correspondance, l'opération est bloquée)."""
        while True:
            with self.lock:
                for tpl in self.tuples:
                    if self.match(template, tpl):
                        return tpl

    def rdp(self, template):
        """Une version non bloquante de rd (s'il n'y a pas de correspondance, un message d'erreur est renvoyé)."""
        for tpl in self.tuples:
            if self.match(template, tpl):
                return tpl
        return None

    def add(self, tpl):
        with self.lock:
            for i, t in enumerate(self.tuples):
                if t[0] == tpl[0]:
                    self.tuples[i] = tpl
                    return

    @staticmethod
    def match(template, tpl):
        for i in range(0, len(template)):
            if template[i] is not None and template[i] != tpl[i]:
                return False
        return True
 ```

 ## Implémentation de l'application

 ### Capteurs

 ### Pompe

 ### Ventilateur

 ### Contrôle des niveaux

 ### Commande du sytème

 ### Agents supplémentaires : l'environnement
 Nous avons dû implémenter, en plus des autres agents, deux nouveaux agents qui se chargent de faire monter les gaz et le niveau d'eau.