# Projet 4 – Serveur DHCP sur routeur Cisco (Router DHCP)

## Description

Ce projet fait partie de mon **challenge “30 Cisco Projects”**.
Après avoir segmenté le réseau avec des **VLANs** et activé le **routage inter-VLAN** (Projet 3), j’ai voulu automatiser l’attribution des adresses IP à l’aide d’un **serveur DHCP intégré au routeur Cisco**.

L’objectif était de permettre à chaque VLAN d’obtenir automatiquement une **adresse IP, une passerelle et un DNS**, sans aucune configuration manuelle.
C’est une étape essentielle pour comprendre comment un réseau d’entreprise moderne distribue dynamiquement les adresses tout en conservant la segmentation et la sécurité.

---

## Contexte et motivation

Dans les projets précédents, chaque poste nécessitait une configuration IP manuelle.
Ce processus est long, répétitif et source d’erreurs lorsqu’on commence à avoir plusieurs dizaines de machines.

J’ai donc décidé d’exploiter le **DHCP intégré au routeur Cisco 2911** afin de :

* Simplifier la configuration des hôtes,
* Automatiser la gestion des sous-réseaux,
* Comprendre en profondeur le fonctionnement des **pools DHCP** et des adresses exclues sur Cisco IOS.

Ce projet rend mon infrastructure **plus professionnelle, scalable et facile à administrer**.

---

## Objectifs techniques

* Mettre en place un **serveur DHCP intégré** au routeur Cisco
* Créer des **pools DHCP distincts** pour les VLANs Direction, RH et IT
* Réserver et exclure les adresses critiques (passerelles, serveurs)
* Permettre aux hôtes de recevoir automatiquement leur configuration réseau
* Vérifier la cohérence du fonctionnement à l’aide des commandes de diagnostic

---

## Topologie réseau

### Architecture utilisée

* 3 postes utilisateurs (Direction, RH, IT)
* 1 switch Cisco 2960
* 1 routeur Cisco 2911 (faisant aussi office de serveur DHCP)

### Plan d’adressage

| VLAN | Département | Réseau /24      | Passerelle   | Plage DHCP distribuée |
| ---- | ----------- | --------------- | ------------ | --------------------- |
| 10   | Direction   | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.11 → .254  |
| 20   | RH          | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.11 → .254  |
| 30   | IT          | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.11 → .254  |

---

## Étapes de configuration

### 1. Préparation du réseau

J’ai réutilisé exactement la même topologie que celle du Projet 3 :
les VLANs, sous-interfaces et trunks étaient déjà opérationnels.

📸 *Schema_DHCP_Before.jpg*

---

### 2. Passage des PC en mode DHCP

Dans chaque poste :
**Desktop → IP Configuration → DHCP**

Les anciennes adresses statiques ont été remplacées automatiquement.

📸 *PC_DHCP_Mode.jpg*

---

### 3. Configuration du serveur DHCP sur le routeur

```bash
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

! VLAN 20 - RH
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
 domain-name rh.local

! VLAN 30 - IT
ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
 domain-name it.local
```

📸 *Show_DHCP_Config_Complete.jpg*

---

### 4. Vérification du fonctionnement du DHCP

Commandes utilisées :

```bash
show ip dhcp binding
show ip dhcp pool
```

📸 *Show_DHCP_Binding.jpg*

Ces commandes m’ont permis de vérifier que :

* chaque pool DHCP était actif,
* les PC recevaient bien une IP correspondante à leur VLAN.

---

### 5. Vérification sur les clients

| Poste     | Adresse obtenue | Passerelle   | DNS     |
| --------- | --------------- | ------------ | ------- |
| Direction | 192.168.10.11   | 192.168.10.1 | 8.8.8.8 |
| RH        | 192.168.20.11   | 192.168.20.1 | 8.8.8.8 |
| IT        | 192.168.30.11   | 192.168.30.1 | 8.8.8.8 |

📸

* PC_Direction_IP_DHCP.jpg
* PC_RH_IP_DHCP.jpg
* PC_IT_IP_DHCP.jpg

---

### 6. Tests de connectivité

J’ai effectué différents tests pour valider :

* la bonne attribution des IP,
* le fonctionnement du routage inter-VLAN,
* la persistance de l’ACL du projet 3 (RH → IT bloqué).

📸

* Ping_OK_DHCP.jpg
* Ping_Blocked_DHCP.jpg

---

## Difficultés rencontrées

* **Masque incorrect dans un pool DHCP**
  Un simple oubli du `/24` provoquait une distribution erronée des adresses.

* **Erreur dans les adresses exclues**
  J’avais mal tapé une adresse, ce qui rendait une passerelle assignable par le DHCP.

* **Un PC ne recevait rien**
  L’interface côté routeur était encore en `shutdown`.
  Après l’avoir réactivée, tout est rentré dans l’ordre.

Ces erreurs m’ont poussé à utiliser systématiquement :

```bash
show ip dhcp binding
show running-config
```

afin de vérifier l’état réel du routeur.

---

## Résultats obtenus

* Attribution automatique des adresses IP pour chaque VLAN.
* Infrastructure DHCP centralisée fonctionnant parfaitement.
* Routage inter-VLAN intact.
* ACL du Projet 3 toujours opérationnelle.
* Réseau désormais géré de manière **professionnelle et automatisée**.

---

## Compétences acquises

* Création et gestion de pools DHCP Cisco
* Exclusion d’adresses réservées
* Vérification et dépannage du DHCP
* Analyse du trafic inter-VLAN dans un environnement segmenté
* Construction d’une architecture réseau cohérente et évolutive

---

## 👤 Auteur

**Nom et Prénom :** Dylan Chriist BEBEY NZEKE  
**Formation :** Bachelor 3 – Administration d’infrastructure sécurisée (ECE Paris)  
**Localisation :** Paris, France  
**Email :** [dylanchriist@gmail.com](mailto:dylanchriist@gmail.com)  
**LinkedIn :** [www.linkedin.com/in/dylan-bebey-012886330/](https://www.linkedin.com/in/dylan-bebey-012886330/)  
**GitHub :** [github.com/DylanBebey](https://github.com/DylanBebey)

---
