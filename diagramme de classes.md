<!-- code à excécuter sur https://mermaid.js.org/syntax/classDiagram.html#syntax -->
<!--  pour mettre un commentaire dans un fichier .md-->
<!-- installer extension Markdown Preview Mermaid Support pour prévisualisation sur Vscode -->

```mermaid
---
title: QR CODE TRACKING API
---
classDiagram
    namespace Core {
        class User {
            +id: int
            +username: str
            +email: str
            +hashed_password: str
            +created_at: datetime
            +is_active: bool
            +create_user()
            +authenticate()
            +get_user_by_email()
        }

        class QRCode {
            +id: int
            +qr_id: str
            +content: str
            +qr_type: str
            +user_id: int
            +original_url: str
            +redirect_url: str
            +created_at: datetime
            +is_active: bool
            +image_path: str
            +generate_simple_qr()
            +generate_tracked_qr()
            +get_qr_by_id()
        }

        class TrackingEvent {
            +id: int
            +qr_code_id: int
            +accessed_at: datetime
            +ip_address: str
            +user_agent: str
            +referer: str
            +country: str
            +city: str
            +record_access()
            +get_stats_by_qr()
        }
    }

    namespace Services {
        class QRCodeGenerator {
            <<utility>>
            +generate_qr_image(content: str): bytes
            +customize_qr_code(content: str, options: dict): bytes
            +save_qr_image(qr_data: bytes, filename: str): str
            +add_logo_to_qr(qr_image: bytes, logo: bytes): bytes
        }

        class AuthManager {
            <<utility>>
            +create_access_token(user_data: dict): str
            +verify_token(token: str): dict
            +hash_password(password: str): str
            +verify_password(password: str, hash: str): bool
            +get_current_user(token: str): User
        }

        class TrackingService {
            <<service>>
            +record_qr_access(qr_id: str, request_data: dict): TrackingEvent
            +get_usage_statistics(qr_id: str, user_id: int): dict
            +generate_analytics_report(qr_id: str): dict
            +get_geographic_stats(qr_id: str): list
            +get_time_based_stats(qr_id: str): dict
        }

        class RedirectService {
            <<service>>
            +process_qr_redirect(qr_id: str, request: dict): str
            +validate_qr_code(qr_id: str): QRCode
            +log_redirect_attempt(qr_id: str, success: bool): void
        }
    }

    namespace API {
        class QRCodeRouter {
            <<controller>>
            +create_simple_qr(content: str): dict
            +create_tracked_qr(url: str, user: User): dict
            +get_user_qr_codes(user: User): list
            +delete_qr_code(qr_id: str, user: User): dict
        }

        class UserRouter {
            <<controller>>
            +register_user(user_data: dict): dict
            +login_user(credentials: dict): dict
            +get_current_user_info(user: User): dict
            +update_user_profile(user: User, data: dict): dict
        }

        class TrackingRouter {
            <<controller>>
            +redirect_qr_code(qr_id: str, request: dict): redirect
            +get_qr_statistics(qr_id: str, user: User): dict
            +export_statistics(qr_id: str, user: User): file
        }
    }

    %% Relations principales
    User "1" --> "*" QRCode : owns
    QRCode "1" --> "*" TrackingEvent : generates
    
    %% Relations de service
    QRCodeGenerator ..> QRCode : creates
    AuthManager ..> User : authenticates
    TrackingService ..> TrackingEvent : processes
    TrackingService ..> QRCode : analyzes
    RedirectService ..> QRCode : redirects
    RedirectService ..> TrackingEvent : logs
    
    %% Relations avec les contrôleurs
    QRCodeRouter ..> QRCodeGenerator : uses
    QRCodeRouter ..> User : requires
    UserRouter ..> AuthManager : uses
    TrackingRouter ..> TrackingService : uses
    TrackingRouter ..> RedirectService : uses
    
    %% Notes
    note for QRCode "qr_type peut être:\n'simple' ou 'tracked'\n\nContent contient:\n- Texte brut (simple QR)\n- URL de redirection (tracked QR)"
    note for TrackingEvent "Enregistre chaque scan\nd'un QR code suivi\n\nInformations collectées:\n- Timestamp\n- Géolocalisation\n- Navigateur"
```
