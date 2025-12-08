# Projet 2 – Segmentation du réseau et sécurisation avec VLANs & ACLs

## Description

Ce projet fait partie de mon **challenge “30 Cisco Projects”**.
Il consiste à concevoir un **réseau d’entreprise segmenté** avec des **VLANs**, puis à le **sécuriser grâce à des ACLs (Access Control Lists)** afin de maîtriser les bases de la **sécurité réseau sur Cisco Packet Tracer**.

L’objectif principal était d’apprendre à :

* Créer et gérer plusieurs VLANs (Direction, RH, IT)
* Configurer le **routage inter-VLAN (Router-on-a-Stick)**
* Mettre en place des **ACLs** pour restreindre la communication entre certains VLANs
* Tester et valider l’ensemble de la connectivité

---

## Contexte et motivation

Avant d’aborder des sujets avancés en cybersécurité, il est essentiel de comprendre la **segmentation réseau** et son lien direct avec la sécurité.

Ce projet m’a permis de :

* simuler un **réseau d’entreprise réaliste**,
* isoler logiquement les départements,
* contrôler précisément les flux autorisés,
* appliquer des politiques de sécurité claires via des ACLs.

Il m’a également permis de mieux comprendre la logique des trames **802.1Q**, utilisée dans la gestion des VLANs sur Cisco.

---

## Objectifs techniques

* Création de **3 VLANs** (Direction – 10, RH – 20, IT – 30)
* Affectation des ports aux VLANs correspondants
* Mise en place d’un **lien trunk 802.1Q** switch → routeur
* Configuration du **router-on-a-stick**
* Application d’une ACL pour bloquer les communications RH → IT
* Vérification de la configuration et des filtrages

---

## Topologie réseau

### Architecture utilisée

* 3 postes clients (Direction, RH, IT)
* 1 switch Cisco 2960
* 1 routeur Cisco 2911

### Plan d’adressage

| VLAN | Département | Réseau          | Passerelle   | Exemple IP    |
| ---- | ----------- | --------------- | ------------ | ------------- |
| 10   | Direction   | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.10 |
| 20   | RH          | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.10 |
| 30   | IT          | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.10 |

---

## Étapes de configuration

### 1. Création des VLANs

```bash
Switch(config)# vlan 10
Switch(config-vlan)# name Direction
Switch(config)# vlan 20
Switch(config-vlan)# name RH
Switch(config)# vlan 30
Switch(config-vlan)# name IT
```

---

### 2. Affectation des ports

```bash
Switch(config)# interface fa0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

Même opération pour RH et IT, en adaptant l’interface.

---

### 3. Configuration du trunk

```bash
Switch(config)# interface g0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
```

---

### 4. Routage inter-VLAN

```bash
Router(config)# interface g0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
```

Répété pour les VLANs 20 et 30.

---

### 5. Configuration IP des clients

```text
Direction : 192.168.10.10
RH        : 192.168.20.10
IT        : 192.168.30.10
```

---

### 6. ACL de sécurité

Objectif : empêcher RH d’accéder au VLAN IT.

```bash
access-list 100 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
access-list 100 permit ip any any
interface g0/0.20
ip access-group 100 in
```

---

### 7. Vérifications

Commandes utilisées :

```bash
show vlan brief
show ip interface brief
show access-lists
```

Tests réalisés :

* RH → IT : refusé
* Direction → tous : autorisé
* IT → RH : autorisé

---

## Difficultés rencontrées

### 1. ACL appliquée au mauvais endroit

Initialement appliquée en sortie (`out`), l’ACL ne fonctionnait pas.
J’ai corrigé en l’appliquant **en entrée** sur la sous-interface du VLAN 20.

### 2. Problème de trunk

Le trunk était configuré mais les VLANs n’étaient pas autorisés → trafic bloqué.
Après ajout de la ligne :

```bash
switchport trunk allowed vlan 10,20,30
```

les VLANs sont passés correctement.

### 3. Mauvais masque sur un PC

Un client utilisait un masque erroné (255.255.0.0).
Une fois corrigé, les pings inter-VLAN fonctionnaient.

Ces erreurs m’ont obligé à analyser couche par couche (modèle OSI), ce qui m’a permis de mieux comprendre le fonctionnement global du réseau.

---

## Résultats obtenus

* Routage inter-VLAN parfaitement fonctionnel
* ACL bloquant RH → IT comme prévu
* Réseau segmenté et sécurisé
* Documentation complète

---

## Compétences développées

* Gestion avancée des VLANs
* Mise en place de trunk 802.1Q
* Routage inter-VLAN
* Conception d’ACL Cisco
* Analyse et diagnostic réseau
* Rédaction technique

---

## Organisation du projet

```
Projet2-VLAN-Securite/
├── configurations/
│   ├── R1_running-config.txt
│   └── SW1_running-config.txt
├── docs/
│   └── schema_logique.png
├── projet2_vlan_securite.pkt
└── README.md
```

---

## Auteur

👤 **Dylan CHRIIST BEBEY NZEKE**
Bachelor 3 – Administration d’infrastructure sécurisée (ECE Paris)
Paris, France
[dylanchriist@gmail.com](mailto:dylanchriist@gmail.com)
LinkedIn : [https://www.linkedin.com/in/dylan-bebey-012886330/](https://www.linkedin.com/in/dylan-bebey-012886330/)
GitHub : [https://github.com/DylanBebey](https://github.com/DylanBebey)

---
