<!-- code à excécuter sur https://mermaid.js.org/syntax/classDiagram.html#syntax -->
<!--  pour mettre un commentaire dans un fichier .md-->
<!-- installer extension Markdown Preview Mermaid Support pour prévisualisation sur Vscode -->

```mermaid
---
title: API DE SUIVI QR CODE
---
classDiagram
    namespace Noyau {
        class Utilisateur {
            +id: int
            +nom_utilisateur: str
            +email: str
            +mot_de_passe_hache: str
            +date_creation: datetime
            +est_actif: bool
            +creer_utilisateur()
            +authentifier()
            +obtenir_utilisateur_par_email()
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
            +generer_qr_simple()
            +generer_qr_suivi()
            +obtenir_qr_par_id()
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
            +enregistrer_acces()
            +obtenir_stats_par_qr()
        }
    }

    namespace Services {
        class GenerateurQR {
            <<utilitaire>>
            +generer_image_qr(contenu: str): bytes
            +personnaliser_code_qr(contenu: str, options: dict): bytes
            +sauvegarder_image_qr(donnees_qr: bytes, nom_fichier: str): str
            +ajouter_logo_au_qr(image_qr: bytes, logo: bytes): bytes
        }

        class GestionnaireAuth {
            <<utilitaire>>
            +creer_jeton_acces(donnees_utilisateur: dict): str
            +verifier_jeton(jeton: str): dict
            +hacher_mot_de_passe(mot_de_passe: str): str
            +verifier_mot_de_passe(mot_de_passe: str, hache: str): bool
            +obtenir_utilisateur_actuel(jeton: str): Utilisateur
        }

        class ServiceSuivi {
            <<service>>
            +enregistrer_acces_qr(id_qr: str, donnees_requete: dict): EvenementSuivi
            +obtenir_statistiques_usage(id_qr: str, id_utilisateur: int): dict
            +generer_rapport_analytique(id_qr: str): dict
            +obtenir_stats_geographiques(id_qr: str): list
            +obtenir_stats_temporelles(id_qr: str): dict
        }

        class ServiceRedirection {
            <<service>>
            +traiter_redirection_qr(id_qr: str, requete: dict): str
            +valider_code_qr(id_qr: str): CodeQR
            +journaliser_tentative_redirection(id_qr: str, succes: bool): void
        }
    }

    namespace API {
        class RouteurQR {
            <<controleur>>
            +creer_qr_simple(contenu: str): dict
            +creer_qr_suivi(url: str, utilisateur: Utilisateur): dict
            +obtenir_qr_utilisateur(utilisateur: Utilisateur): list
            +supprimer_code_qr(id_qr: str, utilisateur: Utilisateur): dict
        }

        class RouteurUtilisateur {
            <<controleur>>
            +inscrire_utilisateur(donnees_utilisateur: dict): dict
            +connecter_utilisateur(identifiants: dict): dict
            +obtenir_info_utilisateur_actuel(utilisateur: Utilisateur): dict
            +mettre_a_jour_profil(utilisateur: Utilisateur, donnees: dict): dict
        }

        class RouteurSuivi {
            <<controleur>>
            +rediriger_code_qr(id_qr: str, requete: dict): redirect
            +obtenir_statistiques_qr(id_qr: str, utilisateur: Utilisateur): dict
            +exporter_statistiques(id_qr: str, utilisateur: Utilisateur): file
        }
    }

    %% Relations principales
    Utilisateur "1" --> "*" CodeQR : possède
    CodeQR "1" --> "*" EvenementSuivi : génère
    
    %% Relations de service
    GenerateurQR ..> CodeQR : crée
    GestionnaireAuth ..> Utilisateur : authentifie
    ServiceSuivi ..> EvenementSuivi : traite
    ServiceSuivi ..> CodeQR : analyse
    ServiceRedirection ..> CodeQR : redirige
    ServiceRedirection ..> EvenementSuivi : journalise
    
    %% Relations avec les contrôleurs
    RouteurQR ..> GenerateurQR : utilise
    RouteurQR ..> Utilisateur : requiert
    RouteurUtilisateur ..> GestionnaireAuth : utilise
    RouteurSuivi ..> ServiceSuivi : utilise
    RouteurSuivi ..> ServiceRedirection : utilise
    
    %% Notes
    note for CodeQR "type_qr peut être:\n'simple' ou 'suivi'\n\nContenu contient:\n- Texte brut (QR simple)\n- URL de redirection (QR suivi)"
    note for EvenementSuivi "Enregistre chaque scan\nd'un QR code suivi\n\nInformations collectées:\n- Horodatage\n- Géolocalisation\n- Navigateur"
```
