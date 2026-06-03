# Atelier — Boussole FinOps
### Construire un système FinOps multi-agents sur Amazon Bedrock AgentCore, en équipe, avec Kiro

> **STATUT : BROUILLON À VALIDER.**
> Ce document décrit le déroulé de l'atelier et la **répartition des responsabilités**.
> Les fichiers Terraform (`iac/`), le code des agents et des outils, et les jeux de données
> simulés **seront générés après validation de ce plan**.
>
> **Ce n'est pas un « lab » classique** : c'est un **workshop d'équipe (2 à 5 personnes)**,
> centré sur la construction d'une solution agentique **avec Kiro**.

---

## 0. Décisions actées

| Sujet | Décision |
|---|---|
| **Format** | **Workshop d'équipe** (2 à 5 personnes). Pas un atelier individuel. On évite le mot « lab » dans le guide. |
| **Identifiants** | **Pas de trigramme.** Les ressources sont namespacées **par équipe** via le préfixe `${EQUIPE}` (ex. `team-01`), attribué par le formateur. |
| **Région** | `eu-west-1` (Irlande) par défaut. |
| **Plateforme agents** | **Amazon Bedrock AgentCore** — Runtime, Gateway, Memory, Identity, Observability, Policy. |
| **Co-construction** | **Kiro** est l'IDE agentique utilisé pour écrire les outils et la logique des agents. |
| **Nombre d'agents** | **Trois** : Argus (analyste, RO), Sibylle (prévision + alerte), Faucon (action sous garde-fous). |
| **Sources de données** | CUR, AWS Organizations, CloudWatch, Trusted Advisor (**checks gratuits uniquement**), API externe de licences SaaS, on-prem (**jeu simulé**). |
| **Périmètre d'action** | Faucon n'arrête que des EC2/RDS taguées `env=dev`. Trois garde-fous : Policy + dry-run + validation humaine. |
| **Notifications** | Amazon SNS (topic `${EQUIPE}-finops-alerts`) pour les alertes de Sibylle et les validations de Faucon. |
| **Terraform (`iac/`)** | Exécuté **uniquement par le formateur**. Les équipes n'exécutent **jamais** de Terraform. |
| **IAM** | **100 % géré par Terraform** afin qu'aucune équipe ne rencontre d'erreur *Access Denied*. |
| **Coquilles** | Agents et outils **pré-créés** avec un code **à trous (TODO)** ; l'équipe complète la logique dans Kiro. |
| **Données** | **Jeux simulés** (CUR, budgets, on-prem, licences) représentatifs de Méridian. Pas de coût réel ni de dérive. |
| **Nettoyage** | **`terraform destroy`** exécuté par le formateur en fin de workshop. |

---

## 1. Intention pédagogique (double)

1. **Architecture FinOps agentique sur AgentCore** — exposer des sources comme **outils**
   (Gateway), héberger des agents (Runtime), garder le contexte (Memory), authentifier
   (Identity), encadrer l'action (Policy) et tout tracer (Observability). Le tout en
   raisonnant **TCO** : AWS multi-comptes + on-prem + licences SaaS.
2. **Construire avec Kiro** — utiliser Kiro comme **co-constructeur** : cartographier le
   dépôt, échafauder les outils, écrire la logique des agents, expliquer le code, itérer.

> On va **au-delà** de l'analyse de coût native (Cost Explorer assisté par IA) : vision TCO
> unifiée hors frontières AWS, **prévision + alerte proactive**, **et** un agent qui **agit**.

---

## 2. Scénario

> **Groupe Méridian**, scale-up française de logistique / e-commerce (15 → 400 personnes en
> 3 ans), opère une **Organisation AWS de plusieurs dizaines de comptes**, un **datacenter
> on-premise** en décommissionnement et une **forêt d'abonnements SaaS**. La facture AWS a
> **doublé en un an** ; des environnements de démo tournent la nuit et le week-end ; les SaaS
> s'empilent sans propriétaire. **Inès Khelifi** (CFO) veut une vue « tout compris » ; **Tom
> Riveiro** (FinOps) a les données mais une exploitation manuelle et cloisonnée.
>
> **Mission de l'équipe :** doter Méridian d'une **Boussole FinOps** multi-agents — voir
> (Argus), anticiper (Sibylle), agir (Faucon) — construite avec Kiro.

---

## 3. Répartition des rôles dans l'équipe

| Rôle | Agent | Responsabilité |
|---|---|---|
| 👁️ **Équipe Argus** | Argus (analyste, RO) | Branche les outils, construit l'agent qui répond aux 4 questions FinOps. |
| 🔮 **Équipe Sibylle** | Sibylle (prévision) | Projection fin de mois, écart au budget, alerte SNS. |
| 🦅 **Équipe Faucon** | Faucon (action) | Arrêt EC2/RDS `env=dev` sous Policy + dry-run + validation. |

- **Étapes 1 et 2 (socle + outils) : ensemble.** Les outils sont communs aux trois agents.
- **Étapes 3-4-5 : en parallèle** par binôme (ou **en séquence** si l'équipe compte 2 personnes).
- **Étape 6 (orchestration + démo) : ensemble.**

---

## 4. Déroulé des étapes (web guide)

1. **Prendre en main AgentCore & Kiro** — tour du socle déployé, ouverture du dépôt dans Kiro.
2. **Outils via Gateway** — compléter et enregistrer les 6 outils (contrat de sortie commun).
3. **Argus** — system prompt, déclaration d'outils, 4 intentions, Memory, test conversationnel.
4. **Sibylle** — projection CUR, comparaison au budget, alerte SNS, exécution planifiée.
5. **Faucon** — tag `env=dev`, dry-run, human-in-the-loop, exécution + audit, test de refus.
6. **Orchestrer & tester** — boucle Voir → Anticiper → Agir de bout en bout + restitution.

---

## 5. Responsabilités formateur / équipe

- **Formateur** : déploie le socle Terraform (AgentCore, IAM, SNS, Gateway, coquilles des
  agents/outils, parc de démo EC2/RDS tagué `env=dev` et `env=prod`, jeux de données simulés),
  attribue un préfixe `${EQUIPE}` par équipe, et exécute `terraform destroy` à la fin.
- **Équipe** : complète **uniquement** la logique des agents et des outils, avec Kiro.
  N'exécute **jamais** de Terraform, ne doit rencontrer **aucune** erreur *Access Denied*.

---

## 6. À produire après validation

- `index.html` + `styles.css` + `script.js` — **le guide web est déjà produit** (aligné sur
  le style de l'atelier « Galerie Imaginaire »).
- `images/architecture.md` — **déjà produit** (diagramme mermaid du système multi-agents).
- `iac/` — socle Terraform (formateur).
- `tools/` — coquilles des 8 outils (cur, organizations, cloudwatch, trusted_advisor,
  licences, onprem, notification, remediation) avec TODO.
- `agents/` — coquilles d'Argus, Sibylle, Faucon (prompts + logique à trous).
- `data/` — jeux de données simulés (cur.json, budgets.json, onprem.json, mock licence API).
- `tests/` — tests de référence, dont le **test de refus** de Faucon (cible `env=prod`).
