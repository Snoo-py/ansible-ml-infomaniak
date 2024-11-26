# ansible-ml-infomaniak
Déploiement d'un serveur mailman pour gérer les mailing listes avec infomaniak

## Inspiration
- [Créer des listes de discussion avec Public Cloud](https://www.infomaniak.com/fr/support/faq/2612/creer-des-listes-de-discussion-avec-public-cloud)
- [infomaniak mailman-cloud](https://github.com/reneluria/mailman-cloud)
- [docker mailman](https://github.com/maxking/docker-mailman)

## Logiciels utilisés
* **mailman3** pour la gestion des mailing listes
* **postfix** pour le serveur smtp
* **fetchmail** pour récupérer les mail dans la boîte mail d'infomaniak
* **traefik** frontale web et la gestion des certificats letsencrypt
* **nginx** pour servir les fichiers statics

## Schéma
### Principe de fonctionnement
![overview](https://github.com/Snoo-py/ansible-ml-infomaniak/blob/main/docs/mailman_overview.png?raw=true)

### Sequence
![sequence](https://github.com/Snoo-py/ansible-ml-infomaniak/blob/main/docs/mailman_flow.png?raw=true)

## Variables

> [!IMPORTANT]
> Les **variables** en gras doivent être obligatoirement définies.

Variables pour configurer mailman3, fetchmail et postfix.
| Variables | Valeur par défaut | Remarques |
| --------- | ----------------- | --------- |
| mailman3_app_dir | /opt/mailman3/ | Répertoire où va être déployé le docker compose |
| **mailman3_domain** | | Votre nom de domaine (eg: domain.xyz) |
| mailman3_vhost | mailman.{{ mailman3_domain }} | l'adresse pour accéder à l'interface web mailman3 |
| mailman3_admin_user | admin | L'utilisateur admin pour se connecter à mailman3 |
| **mailman3_admin_email** | | L'adresse email de l'utilisateur admin (utilisé pour récupérer le mot de passe) |
| **mailman3_acme_email** | | email pour le chalenge letsencrypt |
| mailman3_postfix_relayhost | mail.infomaniak.com | |
| mailman3_mail_account | mailman@{{ mailman3_domain }} | |
| **mailman3_mail_password** | | Le mot de passe utilisé pour créer l'adresse mailman sur infomaniak |
| **mailman3_web_secret_key** | "CHANGEME" | |
| **mailman3_database_password** | "CHANGEME" | |
| **mailman3_api_key** | "CHANGEME" | |

Variables des versions des images dockers
| Variables | Valeur par défaut | Remarques |
| --------- | ----------------- | --------- |
| mailman3_traefik_version | v3.2 | |
| mailman3_nginx_version | latest | |
| mailman3_mailman_core_version | 0.4 | Use a specific version tag (tag latest is not published) |
| mailman3_mailman_web_version | 0.4 | Use a specific version tag (tag latest is not published) |
| mailman3_postgres_version | 12-alpine | |

## Déploiement

1. Créer votre serveur sur le Public Cloud d'Infomaniak en mettant bien votre clé ssh dessus.
2. Créer un enregistrement DNS mailman.{{ votre_domaine }} qui pointe vers l'ip du serveur créé.
3. Dans le fichier **inventory** et **playbook.yml** remplacer mailman.domain.xyz par le DNS de votre serveur ( mailman.{{ votre_domaine }} ).
4. Dans le répertoire **host_vars**, renomer le répertoire mailman.domain.xyz avec le DNS de votre serveur ( mailman.{{ votre_domaine }} ).
5. Modifier les variables du fichier dans **host_vars/mailman.{{ votre_domaine }/vars** par vos valeurs.
6. Executer le **playbook.yml** `ansible-playbook playbook.yml`.