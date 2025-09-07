```{mermaid}
classDiagram
    class Magasin {
        -str nom
        -List[Legume] stock
        -List[Client] clients
    }

    class Legume {
        -str type_leg
        -str variete
        -float prix
        -float stock
    }

    class Client {
        -int numeroClient
        -str nom
        -str prenom
        -int age
        -bool carteFidelite
        -List[Commande]
        +void validerCommande(int numeroCommande)
    }

    class Commande {
        -int numeroClient
        -int numeroCommande
        -float montant
        -List[LigneCommande] lignesCommande
    }

    class LigneCommande {
        -Legume legume
        -float quantite
    }

    Magasin o-- Client : gére
    Client --> Commande : réalise
    Commande o-- LigneCommande : composée
    LigneCommande -- Legume
    Magasin o-- Legume : stocke
```
