# Projet 1 — LAN d’entreprise sous Cisco Packet Tracer

*Status : Terminé • Dossier : `Projet1-LAN-Cisco/` • Fichier PT : `lan_base.pkt`*

---

## 1. Introduction

Ce projet inaugure mon **30 Cisco Projects Challenge**, un parcours personnel que j’ai lancé pour renforcer mes compétences en **réseaux Cisco** et en **cybersécurité**.
L’objectif de ce premier projet est de construire un **réseau local (LAN)** d’entreprise minimaliste mais fonctionnel, avec **adressage IP statique**, **connectivité validée** et **documentation complète**.

---

## 2. Objectifs

* Créer une **topologie LAN** (3 postes + 1 switch).
* Configurer les **adresses IP statiques** de chaque poste.
* Tester la **connectivité ICMP (ping)** entre les hôtes.
* Vérifier la **table MAC** du switch.
* Documenter entièrement le projet.

---

## 3. Pourquoi ce projet est important

Ce premier projet pose les **bases fondamentales** du métier d’administrateur réseau :

* Comprendre la **couche 2 (switching)**.
* Manipuler les outils de diagnostic : **ping**, **MAC table**, **statuts d’interface**.
* Apprendre à **documenter un projet technique**, comme en entreprise.

Ce projet constitue également une **première brique essentielle en cybersécurité**, puisqu’on ne peut pas sécuriser un réseau que l’on ne maîtrise pas techniquement.

---

## 4. Topologie & Plan d’adressage

```
PC1 (Poste_RH)  ─┐
PC2 (Poste_IT)  ──┤── Switch Cisco 2960
PC3 (Poste_DIR) ─┘
```

| Hôte   | Nom        | Adresse IP   | Masque        | Passerelle |
| ------ | ---------- | ------------ | ------------- | ---------- |
| PC1    | Poste_RH   | 192.168.10.1 | 255.255.255.0 | —          |
| PC2    | Poste_IT   | 192.168.10.2 | 255.255.255.0 | —          |
| PC3    | Poste_DIR  | 192.168.10.3 | 255.255.255.0 | —          |
| Switch | Switch_LAN | —            | —             | —          |

Aucune passerelle n’est nécessaire : il s’agit d’un **LAN simple sans routage**.

---

## 5. Prérequis

* Connaître les bases : adressage IP, masque, ping.
* Savoir manipuler les équipements dans **Cisco Packet Tracer**.

---

## 6. Outils utilisés

* Cisco Packet Tracer 8.2+
* Switch Cisco 2960
* 3 PC utilisateurs
* Poste Windows / Linux

---

## 7. Organisation du dépôt

```
Projet1-LAN-Cisco/
├── captures/
│   ├── topologie_lan.png
│   ├── ip_pc1.png
│   ├── ip_pc2.png
│   ├── ip_pc3.png
│   ├── ping_pc1_pc2.png
│   ├── ping_pc2_pc3.png
│   ├── show_interfaces.png
│   └── mac_table.png
├── configurations/
│   └── notes_cisco.txt
├── lan_base.pkt
└── README.md
```

---

## 8. Définitions rapides

* **LAN** : réseau local, limité géographiquement.
* **Switch (couche 2)** : appareil qui apprend les **MAC** et relie les hôtes.
* **ICMP / ping** : permet de tester la communication entre équipements.

---

## 9. Étapes pratiques

### Étape 1 — Créer la topologie

1. Ajouter 3 PC.
2. Ajouter un switch 2960.
3. Relier chaque PC au switch via câble **droit (straight-through)** :

   * PC1 → Fa0/1
   * PC2 → Fa0/2
   * PC3 → Fa0/3

📸 Capture : `captures/topologie_lan.png`

---

### Étape 2 — Renommer les hôtes

* PC1 → Poste_RH
* PC2 → Poste_IT
* PC3 → Poste_DIR
* Switch → Switch_LAN

---

### Étape 3 — Configurer les adresses IP

Depuis chaque PC : **Desktop → IP Configuration**

| PC  | IP           | Masque        | Passerelle |
| --- | ------------ | ------------- | ---------- |
| PC1 | 192.168.10.1 | 255.255.255.0 | —          |
| PC2 | 192.168.10.2 | 255.255.255.0 | —          |
| PC3 | 192.168.10.3 | 255.255.255.0 | —          |

📸 Captures :
`ip_pc1.png`, `ip_pc2.png`, `ip_pc3.png`

---

### Étape 4 — Vérifier les liens

```
Switch> enable
Switch# show interfaces status
```

📸 `captures/show_interfaces.png`

---

### Étape 5 — Tester la connectivité

Depuis PC1 :

```
ping 192.168.10.2
ping 192.168.10.3
```

Depuis PC2 :

```
ping 192.168.10.3
```

📸 Captures :
`ping_pc1_pc2.png`, `ping_pc2_pc3.png`

---

### Étape 6 — Vérifier la table MAC

```
Switch# show mac address-table
```

📸 Capture : `captures/mac_table.png`

---

## 10. Tests de validation

* IP correctement attribuées
* Interfaces actives
* Ping OK dans les trois sens
* 3 entrées MAC apprises

---

## 11. Dépannage

### Ping échoué ?

* Vérifier les câbles.
* Vérifier les IP.
* Attendre quelques secondes après le branchement.

### Table MAC vide ?

* Lancer un ping : la table se remplit automatiquement.

---

## 12. Livrables

| Type          | Fichier / Dossier                |
| ------------- | -------------------------------- |
| Packet Tracer | `lan_base.pkt`                   |
| Captures      | `ip_pc1.png`,`ip_pc2.png`,`ip_pc3.png`,`ping_pc1_pc2.png`,`ping_pc1_pc3.png`,`show_interfaces.png`,`mac_table.png`                 |
| Notes Cisco   | `configurations/notes_cisco.txt` |


---

## 13. Commandes utilisées

```
Switch> enable
Switch# show interfaces status
Switch# show mac address-table
```

---

## 14. Résultats attendus

* Pings réussis
* Apprentissage MAC correct du switch

---

## 👤 Auteur

**Dylan Chriist BEBEY NZEKE**
Bachelor 3 – Administration d’infrastructure sécurisée (ECE Paris)
Paris, France
Email : [dylanchriist@gmail.com](mailto:dylanchriist@gmail.com)
LinkedIn : [https://www.linkedin.com/in/dylan-bebey-012886330/](https://www.linkedin.com/in/dylan-bebey-012886330/)
GitHub : [https://github.com/DylanBebey](https://github.com/DylanBebey)

---
