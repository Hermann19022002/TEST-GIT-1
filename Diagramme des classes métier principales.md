<!-- Diagramme des classes métier principales uniquement -->
<!-- code à excécuter sur https://mermaid.js.org/syntax/classDiagram.html#syntax -->
```mermaid
---
title: CLASSES MÉTIER - API DE SUIVI QR CODE
---
classDiagram
    class Utilisateur {
        +id: int
        +nom_utilisateur: str
        +email: str
        +mot_de_passe_hache: str
        +date_creation: datetime
        +date_derniere_connexion: datetime
        +creer_utilisateur()
        +authentifier()
        +obtenir_utilisateur_par_email()
        +desactiver_compte()
        +changer_mot_de_passe()
    }

    class CodeQR {
        +id: int
        +id_qr: str
        +contenu: str
        +type_qr: str
        +id_utilisateur: int
        +url_originale: str
        +url_redirection: str
        +date_creation: datetime
        +est_actif: bool
        +chemin_image: str
        +titre: str
        +description: str
        +generer_qr_simple()
        +generer_qr_suivi()
        +obtenir_qr_par_id()
        +desactiver_qr()
        +modifier_destination()
        +obtenir_url_complete_redirection()
    }

    class EvenementSuivi {
        +id: int
        +id_code_qr: int
        +date_acces: datetime
        +adresse_ip: str
        +agent_utilisateur: str
        +referent: str
        +pays: str
        +ville: str
        +navigateur: str
        +systeme_exploitation: str
        +appareil_mobile: bool
        +enregistrer_acces()
        +obtenir_stats_par_qr()
        +obtenir_stats_par_periode()
        +compter_acces_uniques()
        +analyser_geolocalisation()
    }

    %% Relations principales
    Utilisateur "1" --> "*" CodeQR : possède
    CodeQR "1" --> "*" EvenementSuivi : génère lors des scans
    
   
```
