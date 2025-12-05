# Projet 5 – Mise en place d’un Serveur DNS & Web interne avec filtrage HTTP (Cisco)

## 1. Description

Ce projet constitue la cinquième étape de mon parcours “30 Cisco Projects”.
L’objectif était d’introduire des services réseau réels dans l’architecture déjà construite aux projets 2, 3 et 4 :

* Déployer un **serveur DNS interne**
* Héberger un **site Web intranet**
* Permettre la résolution du domaine interne `intranet.lab.local`
* Mettre en place un **filtrage HTTP** pour contrôler l’accès au site selon les VLANs

Ce projet se rapproche fortement d’un environnement d’entreprise, où l’on combine **services**, **routage**, **VLANs**, et **sécurité réseau**.

---

## 2. Objectifs techniques

* Installation d’un serveur DNS pour le domaine interne `lab.local`
* Ajout d’un enregistrement A : `intranet.lab.local → 192.168.30.100`
* Publication d’un site Web interne via HTTP
* Configuration du serveur pour être accessible sur le réseau VLAN 30
* Mise en place d’une ACL interdisant l’accès HTTP au VLAN RH (VLAN 20)
* Vérification de la résolution DNS, de l’accès Web et de l’application de l’ACL

---

## 3. Topologie réseau

### Matériel :

* Routeur Cisco 2911
* Switch Cisco 2960
* 3 postes clients (Direction, RH, IT)
* 1 serveur (DNS + HTTP)

### Plan d’adressage :

| VLAN | Service         | Réseau /24      | Passerelle   | Particularité           |
| ---- | --------------- | --------------- | ------------ | ----------------------- |
| 10   | Direction       | 192.168.10.0/24 | 192.168.10.1 | Accès complet           |
| 20   | RH              | 192.168.20.0/24 | 192.168.20.1 | HTTP restreint          |
| 30   | IT              | 192.168.30.0/24 | 192.168.30.1 | Héberge serveur DNS/Web |
| -    | Serveur DNS/Web | 192.168.30.100  | 192.168.30.1 | DNS + HTTP activés      |

---

## 4. Implémentation et configuration

### 4.1. Configuration du serveur (VLAN 30)

Adresse IP du serveur :

```
IP : 192.168.30.100
Masque : 255.255.255.0
Passerelle : 192.168.30.1
DNS : 127.0.0.1
```

Capture associée : **Server_IP_Config.jpg**

---

### 4.2. Configuration DNS

Enregistrement ajouté :

| Nom                | Type | Valeur         |
| ------------------ | ---- | -------------- |
| intranet.lab.local | A    | 192.168.30.100 |

Capture : **Server_DNS_Records.jpg**

---

### 4.3. Configuration du site Web

Une page intranet a été créée sur le serveur HTTP.
Le service HTTP a été activé.

Capture : **Server_HTTP_Service.jpg**

---

### 4.4. DHCP (héritage du projet 4)

Les postes récupèrent automatiquement :

* IP du bon VLAN
* Gateway
* DNS interne `192.168.30.100`

Capture : **Show_DHCP_Pools_DNS.jpg**

---

### 4.5. Vérification DNS

Commande :

```
nslookup intranet.lab.local
```

Résultat attendu :
Réponse correcte du serveur 192.168.30.100.

Capture : **PC_NSLOOKUP_OK.jpg**

---

### 4.6. Test de connectivité

```
ping intranet.lab.local
```

Capture : **Ping_OK_Gateway.jpg**

---

### 4.7. Test HTTP

* Direction → accès autorisé
  Capture : **PC_HTTP_OK_Direction.jpg**

* IT → accès autorisé

* RH → **accès bloqué si ACL activée**
  Capture : **PC_HTTP_Blocked_RH.jpg**

---

### 4.8. Configuration de l’ACL HTTP

Objectif : bloquer uniquement le trafic HTTP du VLAN RH vers le serveur web.

Configuration appliquée :

```bash
access-list 120 deny tcp 192.168.20.0 0.0.0.255 host 192.168.30.100 eq 80
access-list 120 permit ip any any

interface g0/0.20
 ip access-group 120 in
```

Capture : **ShowAccessLists.jpg**

---

## 5. Vérifications globales

* **show vlan brief**
  Capture : *ShowVLAN_Brief.jpg*

* **show interfaces trunk**
  Capture : *ShowInterfaces_Trunk.jpg*

* **show ip interface brief** (routeur)
  Capture : *ShowIPInterfaceBrief_R1.jpg*

* **show ip route**
  Capture : *ShowIPRoute_R1.jpg*

---

## 6. Difficultés rencontrées

* Une première résolution DNS échouait à cause d’un enregistrement incorrect
* L’HTTP était inaccessible car le service n’était pas activé
* Les PCs ne résolvaient pas le domaine tant que le DNS n’était pas intégré au DHCP
* L’ACL HTTP bloquait trop largement avant d’être limitée au port 80

Ces erreurs ont permis de revoir la logique DNS, la chaîne de résolution, et le fonctionnement précis d’une ACL ciblée.

---

## 7. Résultats obtenus

* DNS interne parfaitement fonctionnel
* Résolution du domaine `intranet.lab.local` depuis les postes
* Site web interne accessible pour Direction et IT
* Accès RH → HTTP bloqué comme prévu
* Infrastructure désormais capable d’héberger des services internes
* Documentation complète et reproductible

---

## 8. Structure du projet

```
Projet5-DNS-Web/
│
├── captures/
│   ├── PC_HTTP_Blocked_RH.jpg
│   ├── PC_HTTP_OK_Direction.jpg
│   ├── PC_NSLOOKUP_OK.jpg
│   ├── Ping_OK_Gateway.jpg
│   ├── Schema_DNSWeb_Before.jpg
│   ├── Server_DNS_Records.jpg
│   ├── Server_HTTP_Service.jpg
│   ├── Server_IP_Config.jpg
│   ├── Show_DHCP_Pools_DNS.jpg
│
├── configurations/
│   ├── R1_running-config.txt
│   ├── SW1_running-config.txt
│
└── projet5_dns_web.pkt
```

---

## 9. Auteur

**Dylan CHRIIST BEBEY NZEKE**
Bachelor 3 – Cybersécurité & Réseaux (ECE Paris)
Paris, France
GitHub : [https://github.com/DylanBebey](https://github.com/DylanBebey)
LinkedIn : [https://linkedin.com/in/dylan-bebey-012886330](https://linkedin.com/in/dylan-bebey-012886330)

---
