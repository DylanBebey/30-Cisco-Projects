# 🧱 Projet 2 – Segmentation du réseau et sécurisation avec VLANs & ACLs

## 🧩 Description

Ce projet fait partie de mon **challenge “30 Cisco Projects”**.
Il consiste à concevoir un **réseau d’entreprise segmenté** avec des **VLANs** et à le **sécuriser avec des ACLs (Access Control Lists)** afin de maîtriser les bases de la **sécurité réseau sur Cisco Packet Tracer**.

L’objectif principal était d’apprendre à :

* Créer et gérer plusieurs VLANs (Direction, RH, IT)
* Configurer le **routage inter-VLAN (Router-on-a-Stick)**
* Mettre en place des **règles ACLs** pour restreindre les communications
* Tester et vérifier la connectivité entre les différents segments du réseau

---

## 🧠 Contexte et motivation

Avant de parler cybersécurité, il faut **comprendre le réseau** et savoir **le segmenter**.
Ce projet m’a permis de simuler un **réseau d’entreprise réaliste**, où chaque département est isolé et protégé, tout en conservant une communication maîtrisée.
J’ai utilisé **Cisco Packet Tracer** pour représenter cette architecture de manière concrète.

Ce projet m’a aussi sensibilisé à l’importance des **règles d’accès** et à la logique des **trames VLAN (802.1Q)** dans un environnement Cisco.

---

## ⚙️ Objectifs techniques

* Création de **3 VLANs** (Direction, RH, IT)
* Attribution des ports VLAN aux hôtes correspondants
* Configuration d’un **trunk 802.1Q** entre le switch et le routeur
* Mise en place d’un **routage inter-VLAN** via un *Router-on-a-Stick*
* Application d’une **ACL de sécurité** bloquant le VLAN RH vers le VLAN IT
* Vérification de la **connectivité et du filtrage**

---

## 🧭 Topologie réseau

### Architecture :

* 🖥️ **3 postes clients** (1 par service)
* 🧩 **1 switch Cisco 2960**
* 🌐 **1 routeur Cisco 2911**

### Plan d’adressage :

| VLAN | Département | Réseau /24      | Passerelle   | IP Exemple    |
| ---- | ----------- | --------------- | ------------ | ------------- |
| 10   | Direction   | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.10 |
| 20   | RH          | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.10 |
| 30   | IT          | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.10 |

---

## 🔧 Étapes de configuration

### 1️⃣ Création des VLANs

J’ai créé trois VLANs pour séparer logiquement les services de l’entreprise :

```bash
Switch(config)# vlan 10
Switch(config-vlan)# name Direction
Switch(config)# vlan 20
Switch(config-vlan)# name RH
Switch(config)# vlan 30
Switch(config-vlan)# name IT
```

---

### 2️⃣ Affectation des ports aux VLANs

Chaque PC a été assigné à son VLAN respectif :

```bash
Switch(config)# interface fa0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
```

---

### 3️⃣ Configuration du trunk vers le routeur

Ce lien transporte les trames de tous les VLANs :

```bash
Switch(config)# interface g0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30
```

---

### 4️⃣ Routage inter-VLAN (Router-on-a-Stick)

Le routeur a été configuré avec une sous-interface par VLAN :

```bash
Router(config)# interface g0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
```

---

### 5️⃣ Configuration IP des clients

Chaque poste a reçu une IP statique correspondant à son VLAN :

```text
Direction : 192.168.10.10 / 255.255.255.0
RH         : 192.168.20.10 / 255.255.255.0
IT         : 192.168.30.10 / 255.255.255.0
```

---

### 6️⃣ Application d’une ACL de sécurité

L’objectif : bloquer le VLAN RH (20) vers le VLAN IT (30) tout en laissant les autres flux ouverts.

```bash
Router(config)# access-list 100 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
Router(config)# access-list 100 permit ip any any
Router(config)# interface g0/0.20
Router(config-if)# ip access-group 100 in
```

---

### 7️⃣ Vérification et tests

Commandes utilisées pour valider la configuration :

```bash
show vlan brief
show ip interface brief
show access-lists
ping 192.168.30.10
```

Les tests de ping ont confirmé :

* ✅ RH ne peut plus atteindre IT (filtrage ACL réussi)
* ✅ Direction communique avec RH et IT (routage fonctionnel)

---

## ⚠️ Difficultés rencontrées

J’ai rencontré plusieurs problèmes intéressants pendant la configuration :

* ❌ **ACL non fonctionnelle au début** : j’avais appliqué la règle “out” au lieu de “in”. J’ai appris que le bon sens d’application dépend du flux à contrôler.
* ⚙️ **Lien trunk mal configuré** : oubli de préciser les VLANs autorisés, ce qui empêchait le trafic entre VLANs.
* 🧩 **Masque IP incorrect** sur un poste : les tests de ping échouaient à cause d’une erreur de /24 mal saisie.

Ces erreurs m’ont forcé à **analyser méthodiquement** chaque couche du modèle OSI pour comprendre où le problème se situait.
C’est ce qui rend ce projet formateur : on apprend plus en corrigeant ses erreurs qu’en suivant une simple recette.

---

## 🔍 Résultats obtenus

* ✅ Routage inter-VLAN opérationnel
* ✅ ACL parfaitement fonctionnelle
* ✅ Réseau segmenté et sécurisé
* ✅ Configuration sauvegardée et documentée

Ce projet m’a aidé à **comprendre concrètement la logique des VLANs, ACLs et du Router-on-a-Stick** — des compétences de base en ingénierie réseau et cybersécurité.

---

## 🧠 Compétences acquises

* Création et gestion de VLANs
* Configuration de trunks 802.1Q
* Routage inter-VLAN
* Gestion des ACLs Cisco
* Diagnostic et dépannage réseau
* Documentation technique claire et professionnelle

---

## 🗂️ Organisation du projet

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

## 👤 Auteur

**Nom :** Dylan CHRIIST BEBEY NZEKE
🎓 **Formation :** Bachelor 3 – Administration d’infrastructure sécurisée (ECE Paris)
📍 **Localisation :** Paris, France
📧 **Email :** [dylan.chriist.bebey.nzeke@gmail.com](mailto:dylan.chriist.bebey.nzeke@gmail.com)
🔗 **LinkedIn :** [www.linkedin.com/in/dylan-bebey](https://www.linkedin.com/in/dylan-bebey)
💻 **GitHub :** [github.com/DylanBebey](https://github.com/DylanBebey)

---

## 🚀 Prochain projet

Le **Projet 3** portera sur la **mise en place d’un serveur DHCP et DNS dynamique** sur Cisco Packet Tracer.
Cette étape marquera le début de l’automatisation de l’adressage IP et de la gestion des services réseau.


