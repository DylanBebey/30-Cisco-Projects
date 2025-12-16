# Projet 3 – Routage inter-VLAN sur routeur Cisco (Router-on-a-Stick)

## Description

Ce projet s’inscrit dans le cadre de mon **challenge “30 Cisco Projects”**.
Il représente la **suite logique du Projet 2**, où j’avais déjà mis en place la segmentation réseau avec les VLANs et une ACL de sécurité.
Ici, l’objectif est de **formaliser et approfondir le routage inter-VLAN** afin de comprendre **comment un routeur Cisco assure la communication entre plusieurs VLANs** dans un réseau d’entreprise.

Ce projet m’a permis de consolider mes connaissances sur le **Router-on-a-Stick**, les **sous-interfaces**, l’**encapsulation 802.1Q**, et la **logique de routage entre sous-réseaux**.

---

## Objectifs du projet

* Démontrer le fonctionnement du **routage inter-VLAN** sur un routeur Cisco.
* Vérifier la **communication entre VLANs** via des pings croisés.
* Comprendre l’importance du **trunk 802.1Q** et du rôle de chaque **sous-interface**.
* Observer les **tables de routage et d’interfaces** sur le routeur.
* Confirmer l’efficacité de l’**ACL du Projet 2** bloquant RH → IT.
* Produire une documentation complète et claire (captures + analyse).

---

## Topologie réseau

### Architecture

Le réseau utilisé ici est identique à celui du Projet 2 :

* **3 PC** représentant les services : Direction, RH et IT
* **1 Switch Cisco 2960**
* **1 Routeur Cisco 2911**

### Schéma logique

![Schéma logique](./captures/Schema_Logique.jpg)

---

### Plan d’adressage

| VLAN | Département | Réseau /24      | Passerelle   | IP Exemple    |
| ---- | ----------- | --------------- | ------------ | ------------- |
| 10   | Direction   | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.10 |
| 20   | RH          | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.10 |
| 30   | IT          | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.10 |

---

## Étapes de configuration

### 1. Vérification des VLANs existants

```bash
Switch# show vlan brief
```

📸 *Capture :* [ShowVLAN_Brief.jpg](./captures/ShowVLAN_Brief.jpg)

---

### 2. Vérification du trunk entre le switch et le routeur

Le port **GigabitEthernet0/1** est bien en mode **trunk** et transporte les VLANs 10, 20 et 30.

```bash
Switch# show interfaces trunk
```

📸 *Capture :* [ShowInterfaces_Trunk.jpg](./captures/ShowInterfaces_Trunk.jpg)

---

### 3. Configuration du routeur (Router-on-a-Stick)

Le routeur **Router1** a été configuré avec trois **sous-interfaces**, une pour chaque VLAN.

```bash
interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
!
interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
!
interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
```

---

### 4. Vérification des interfaces et du routage

```bash
Router# show ip interface brief
Router# show ip route
```

📸 Captures :

* [ShowIPInterfaceBrief_R1.jpg](./captures/ShowIPInterfaceBrief_R1.jpg)
* [ShowIPRoute_R1.jpg](./captures/ShowIPRoute_R1.jpg)

Les sous-interfaces sont **UP/UP**, et les routes sont bien enregistrées.

---

### 5. Vérification de l’ACL héritée du Projet 2

Cette ACL bloque le VLAN RH (20) vers le VLAN IT (30).

```bash
Router# show access-lists
```

📸 *Capture :* [ShowAccessLists.jpg](./captures/ShowAccessLists.jpg)

---

### 6. Tests de connectivité

#### Ping depuis Direction → RH et IT

Les communications fonctionnent.

📸 *Capture :* [Ping_Direction_to_RH_IT.jpg](./captures/Ping_Direction_to_RH_IT.jpg)

#### Ping depuis RH → IT

Bloqué grâce à l’ACL.

📸 *Capture :* [Ping_RH_to_IT_blocked_if_ACL.jpg](./captures/Ping_RH_to_IT_blocked_if_ACL.jpg)

---

## Analyse du fonctionnement

Ce projet m’a permis d’observer en détail le fonctionnement du **Router-on-a-Stick** :

* Chaque VLAN correspond à un **sous-réseau distinct**.
* Le trunk 802.1Q **transporte les trames VLAN tagguées** vers le routeur.
* Le routeur agit comme **passerelle inter-VLAN** via ses sous-interfaces.
* Les **tables de routage** montrent bien les réseaux directement connectés.
* L’ACL du Projet 2 démontre qu’il est possible de contrôler précisément les communications.

---

## Difficultés rencontrées

* **Trunk mal configuré** : les VLANs n’étaient pas autorisés, empêchant le routage.
* **ACL appliquée au mauvais endroit** : en sortie au lieu de “in”, ce qui la rendait inefficace.
* **Sous-interface inactive** : `no shutdown` manquant sur l'interface physique G0/0.

Ces erreurs m’ont permis de mieux comprendre :

* les mécanismes de l’encapsulation dot1Q,
* la logique du Router-on-a-Stick,
* la manière dont un routeur gère plusieurs sous-réseaux simultanés.

---

## Structure du projet

```
Projet3-InterVLAN-Router/
├── captures/
│   ├── Ping_Direction_to_RH_IT.jpg
│   ├── Ping_RH_to_IT_blocked_if_ACL.jpg
│   ├── Schema_Logique.jpg
│   ├── ShowAccessLists.jpg
│   ├── ShowInterfaces_Trunk.jpg
│   ├── ShowIPInterfaceBrief_R1.jpg
│   ├── ShowIPRoute_R1.jpg
│   └── ShowVLAN_Brief.jpg
├── configurations/
│   ├── R1_running-config.txt
│   └── SW1_running-config.txt
├── projet3_intervlan_router.pkt
└── README.md
```

---

## 👤 Auteur

**Nom et Prénom :** Dylan Chriist BEBEY NZEKE  
**Formation :** Bachelor 3 – Administration d’infrastructure sécurisée (ECE Paris)  
**Localisation :** Paris, France  
**Email :** [dylanchriist@gmail.com](mailto:dylanchriist@gmail.com)  
**LinkedIn :** [linkedin.com/in/dylan-chriist-bebey-nzeke-012886330/](https://www.linkedin.com/in/dylan-chriist-bebey-nzeke-012886330/) 
**GitHub :** [github.com/DylanBebey](https://github.com/DylanBebey)


---
