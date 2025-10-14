# 🌐 Projet 4 – Serveur DHCP sur routeur Cisco (Router DHCP)

## 🧩 Description

Ce projet fait partie de mon **challenge “30 Cisco Projects”**.  
Après avoir segmenté le réseau avec des **VLANs** et activé le **routage inter-VLAN** (Projet 3), j’ai voulu automatiser l’attribution des adresses IP à l’aide d’un **serveur DHCP intégré au routeur Cisco**.

L’objectif de ce projet était de permettre à **chaque VLAN** d’obtenir automatiquement une **adresse IP, une passerelle et un DNS**, sans configuration manuelle.  
C’est une étape essentielle pour comprendre comment un **réseau d’entreprise moderne** distribue dynamiquement les adresses tout en conservant la segmentation et la sécurité.

---

## 🧠 Contexte et motivation

Lors des projets précédents, chaque PC nécessitait une configuration IP manuelle.  
Cela devient vite **long et source d’erreurs** dans un réseau d’entreprise.  

J’ai donc décidé d’intégrer un **serveur DHCP** sur le **routeur Cisco 2911**, afin de :
- Simplifier la configuration des postes,
- Automatiser la gestion des adresses IP,
- Comprendre le fonctionnement des **pools DHCP** et des **adresses exclues** sur Cisco IOS.

Ce projet m’a permis de rendre mon réseau **plus intelligent, évolutif et professionnel**.

---

## ⚙️ Objectifs techniques

- Configuration d’un **serveur DHCP intégré** au routeur Cisco  
- Création de **pools DHCP distincts** pour chaque VLAN (Direction, RH, IT)  
- Exclusion d’adresses utilisées par les passerelles et serveurs  
- Attribution automatique d’adresses IP aux hôtes des VLANs  
- Vérification du fonctionnement via les commandes *show* et les tests *ping*

---

## 🧭 Topologie réseau

### Architecture :

* 🖥️ **3 postes clients** (Direction, RH, IT)  
* 🧩 **1 switch Cisco 2960**  
* 🌐 **1 routeur Cisco 2911** (jouant aussi le rôle de serveur DHCP)

### Plan d’adressage :

| VLAN | Département | Réseau /24      | Passerelle   | Plage DHCP Distribuée   |
| ---- | ------------ | --------------- | ------------- | ----------------------- |
| 10   | Direction    | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.11 → .254    |
| 20   | RH           | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.11 → .254    |
| 30   | IT           | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.11 → .254    |

---

## 🔧 Étapes de configuration

### 1️⃣ Préparation du réseau

J’ai **réutilisé la même topologie** que celle du Projet 3 (avec routage inter-VLAN et ACL déjà en place).  
Aucun changement physique n’a été nécessaire : les VLANs, trunks et sous-interfaces existaient déjà.

📸 **Capture :** `Schema_DHCP_Before.jpg`  
*Topologie de base avant la mise en place du DHCP.*

---

### 2️⃣ Suppression des IP statiques sur les postes

Avant d’activer le DHCP, j’ai configuré chaque PC pour obtenir automatiquement son IP :
> Onglet **Desktop → IP Configuration → DHCP**

📸 **Capture :** `PC_DHCP_Mode.jpg`  
*Un poste configuré pour recevoir automatiquement une adresse.*

---

### 3️⃣ Configuration du serveur DHCP sur le routeur

J’ai configuré un **pool DHCP par VLAN** sur le routeur Cisco :

```bash
Router> enable
Router# configure terminal

! Exclusion des adresses réservées
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10

! VLAN 10 - Direction
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
 domain-name direction.local
 exit

! VLAN 20 - RH
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
 domain-name rh.local
 exit

! VLAN 30 - IT
ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
 domain-name it.local
 exit
```

📸 **Capture :** `Show_DHCP_Config_Complete.jpg`  
*Affichage de la configuration complète du DHCP sur le routeur.*

---

### 4️⃣ Vérification du DHCP actif

Pour vérifier le bon fonctionnement du service :

```bash
show ip dhcp binding
show ip dhcp pool
```

📸 **Capture :** `Show_DHCP_Binding.jpg`  
*Liste des adresses IP attribuées automatiquement aux postes.*

---

### 5️⃣ Vérification sur les clients

Chaque PC a automatiquement reçu une adresse IP correspondant à son VLAN :

| Poste | Adresse obtenue | Passerelle | DNS       |
| ------ | ---------------- | ----------- | ---------- |
| Direction | 192.168.10.11 | 192.168.10.1 | 8.8.8.8 |
| RH | 192.168.20.11 | 192.168.20.1 | 8.8.8.8 |
| IT | 192.168.30.11 | 192.168.30.1 | 8.8.8.8 |

📸 **Captures :**
- `PC_Direction_IP_DHCP.jpg`  
- `PC_RH_IP_DHCP.jpg`  
- `PC_IT_IP_DHCP.jpg`

---

### 6️⃣ Tests de connectivité

Enfin, j’ai effectué plusieurs tests **ping inter-VLAN** pour m’assurer que :
- Le **routage inter-VLAN** fonctionne,
- Le **DHCP** attribue bien des IP valides,
- Et que **l’ACL du projet 3** (RH → IT bloqué) est toujours active.

```bash
ping 192.168.20.10
ping 192.168.30.10
```

📸 **Captures :**
- `Ping_OK_DHCP.jpg` → Ping entre VLANs autorisés  
- `Ping_Blocked_DHCP.jpg` → RH vers IT bloqué (ACL toujours efficace)

---

## ⚠️ Difficultés rencontrées

J’ai rencontré plusieurs obstacles intéressants :

- ❌ **Erreur de pool DHCP** : oubli du masque `/24` → distribution d’adresses incorrecte  
- ⚙️ **Adresses exclues non prises en compte** : faute de frappe corrigée  
- 🧩 **Client ne recevait pas d’adresse** : oubli de mettre l’interface en *no shutdown*  

Ces erreurs m’ont appris à utiliser :
```bash
show ip dhcp binding
show running-config
```
pour diagnostiquer efficacement un problème DHCP.

---

## 🔍 Résultats obtenus

* ✅ Distribution automatique des adresses IP par VLAN  
* ✅ Routage inter-VLAN opérationnel  
* ✅ ACL toujours fonctionnelle  
* ✅ Topologie stable, fonctionnelle et entièrement automatisée  

Grâce à ce projet, j’ai compris comment un routeur Cisco peut jouer le rôle d’un **serveur DHCP centralisé** dans un réseau d’entreprise.

---

## 🧠 Compétences acquises

- Configuration d’un **serveur DHCP intégré sur routeur Cisco**
- Création de **pools DHCP** par VLAN
- Exclusion d’adresses réservées
- Diagnostic et dépannage DHCP (`show ip dhcp binding`)
- Maintien d’une **architecture inter-VLAN dynamique et sécurisée**

---

## 🗂️ Organisation du projet

```
Projet4-DHCP-Router/
├── captures/
│   ├── Schema_DHCP_Before.jpg
│   ├── PC_DHCP_Mode.jpg
│   ├── Show_DHCP_Config_Complete.jpg
│   ├── Show_DHCP_Binding.jpg
│   ├── PC_Direction_IP_DHCP.jpg
│   ├── PC_RH_IP_DHCP.jpg
│   ├── PC_IT_IP_DHCP.jpg
│   ├── Ping_OK_DHCP.jpg
│   └── Ping_Blocked_DHCP.jpg
├── configurations/
│   └── R1_DHCP_Running_Config.txt
├── projet4_dhcp_router.pkt
└── README.md
```

---

## 👤 Auteur

**Nom :** Dylan CHRIIST BEBEY NZEKE  
🎓 **Formation :** Bachelor 3 – Cybersécurité & Réseaux (ECE Paris)  
📍 **Localisation :** Paris, France  
📧 **Email :** [dylanchriist@gmail.com](mailto:dylanchriist@gmail.com)  
🔗 **LinkedIn :** [www.linkedin.com/in/dylan-bebey-012886330](https://www.linkedin.com/in/dylan-bebey-012886330/)  
💻 **GitHub :** [github.com/DylanBebey](https://github.com/DylanBebey)

---

## 🚀 Prochain projet

Le **Projet 5** portera sur la **configuration d’un serveur DNS et Web sur Cisco Packet Tracer**.  
L’objectif sera d’approfondir la logique des services réseaux d’entreprise :  
associer des noms de domaines à des IPs internes, héberger un site web simulé et intégrer ces services à mon infrastructure DHCP.

Ce projet marquera la transition vers la **couche applicative du modèle OSI**, après la maîtrise des couches réseau et transport.

---
