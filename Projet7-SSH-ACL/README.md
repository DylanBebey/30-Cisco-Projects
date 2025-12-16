# **Projet 7 : Sécurisation du routeur par SSH avec ACL (accès réservé au VLAN IT)**

## **1. Description**

Ce projet s’inscrit dans la continuité des projets précédents, où j’ai progressivement construit un réseau d’entreprise complet avec VLANs, routage inter-VLAN, DHCP, DNS, HTTP et sécurité switch.

Dans ce projet 7, l’objectif est de :

* sécuriser l’accès **administratif** d’un routeur,
* en l’autorisant **uniquement via SSH**,
* et en restreignant cet accès **au seul VLAN IT** grâce à une ACL.

Il s’agit d’une bonne pratique essentielle en entreprise : seuls les administrateurs réseau doivent pouvoir accéder aux routeurs, alors que les autres services doivent être totalement bloqués.

---

## **2. Objectifs techniques**

* Activer et configurer SSH sur un routeur dédié.
* Ajouter un routeur d’administration dans la topologie.
* Limiter l’accès SSH au seul VLAN IT (192.168.30.0/24).
* Bloquer SSH pour Direction (192.168.10.0/24) et RH (192.168.20.0/24).
* Tester méthodiquement l’accès SSH, les pings et l’accès HTTP/DNS (.100) avant et après ACL.
* Vérifier la bonne application de l’ACL sur l’interface du routeur.

---

## **3. Architecture utilisée**

Ce projet utilise la même topologie que les projets précédents :

* Switch Cisco 2960
* Routeur principal (Router-on-a-Stick)
* 3 PC utilisateurs :

  * Direction — VLAN 10
  * RH — VLAN 20
  * IT — VLAN 30
* Serveur DNS/WEB — 192.168.30.100
* **Nouveau routeur d’administration : RouterSSH**

### **Câblage**

* RouterSSH est connecté au switch sur `Fa0/5`
* Le port `Fa0/5` a été configuré en **VLAN 30**
* RouterSSH utilise l’adresse :
  **192.168.30.200**

📸 Captures :

* *Switch_Config_FA0_5_VLAN30.jpg*
* *RouterSSH_IP_Config.jpg*

---

## **4. Étapes de configuration**

### **4.1. Configuration du port du switch**

```bash
interface fa0/5
switchport mode access
switchport access vlan 30
```

Objectif : placer RouterSSH dans le **VLAN IT**, seul VLAN autorisé à administrer.

---

### **4.2. Attribution de l’adresse IP sur RouterSSH**

```bash
interface g0/0
ip address 192.168.30.200 255.255.255.0
no shutdown
```

Test des pings AVANT ACL :

* Direction → .200 : **OK**
* RH → .200 : **OK**
* IT → .200 : **OK**

📸 Captures :

* *Ping_Direction_OK.jpg*
* *Ping_RH_Blocked.jpg* (après ACL)

---

### **4.3. Activation de SSH sur RouterSSH**

```bash
hostname RouterSSH
ip domain-name lab.local
crypto key generate rsa modulus 1024
username admin secret MonMotDePasse
line vty 0 4
transport input ssh
login local
ip ssh version 2
```

📸 Capture :

* *RouterSSH_SSH_Config.jpg*

Test SSH AVANT ACL :

* Direction → SSH : **OK**
* RH → SSH : **OK**
* IT → SSH : **OK**

📸 Capture :

* *PC_IT_SSH_OK.jpg*

---

### **4.4. Tests AVANT ACL**

Avant d’appliquer la moindre ACL, j’ai réalisé trois séries de tests :

1. **Ping vers RouterSSH (.200)**

   * Tous les VLANs = **OK**

2. **Ping vers le serveur (.100)**

   * Tous les VLANs = **OK**

3. **SSH vers RouterSSH (.200)**

   * Direction = OK
   * RH = OK
   * IT = OK

Ces tests m’ont permis d’avoir une base claire pour comparer l’effet de l’ACL.

📸 Captures :

* *HTTP_Direction_OK.jpg*
* *PC_IT_SSH_OK.jpg*

---

## **5. Mise en place de l’ACL SSH**

Objectif :
Seul le VLAN IT doit pouvoir accéder à RouterSSH en SSH (port 22).

### **5.1. Création de l’ACL**

```bash
access-list 130 permit tcp 192.168.30.0 0.0.0.255 host 192.168.30.200 eq 22
access-list 130 deny   tcp any host 192.168.30.200 eq 22
access-list 130 permit ip any any
```

Explications :

* La première ligne autorise le VLAN IT (192.168.30.x).
* La seconde ligne bloque SSH depuis Direction & RH.
* La dernière ligne garantit que **tous les autres services** :
  ping, HTTP, DNS restent fonctionnels.

📸 Capture :

* *Capture_ACL_Config.jpg*

---

### **5.2. Application de l’ACL sur RouterSSH**

```bash
interface g0/0
ip access-group 130 in
```

📸 Capture :

* *ACL_Applied_On_G0_0.jpg*

---

## **6. Tests après ACL**

### **6.1. SSH**

* IT → SSH : **OK**
  📸 *PC_IT_SSH_OK.jpg*

* Direction → SSH : **BLOQUÉ**
  📸 *PC_Direction_SSH_Blocked.jpg*

* RH → SSH : **BLOQUÉ**
  📸 *PC_RH_SSH_Blocked.jpg*

### **6.2. Ping**

* Direction → .200 : OK (puis bloqué selon ACL ICMP optionnelle)
* RH → .200 : bloqué si règle ICMP
  📸 *Ping_RH_Blocked.jpg*

### **6.3. HTTP et DNS vers le serveur (.100)**

Très important : l’ACL SSH ne doit PAS casser les services.

Tests :

* Direction → intranet (.100) : **OK**
  📸 *HTTP_Direction_OK.jpg*

* RH → intranet (.100) : OK

* IT → intranet (.100) : OK

---

## **7. Difficultés rencontrées et solutions**

### **7.1. L’ACL bloquait aussi le serveur (192.168.30.100)**

Symptômes :

* Le PC Direction ne pouvait plus accéder à l’intranet en .100.
* Certains pings .100 ou .200 ne passaient plus.

Cause :

* Une version précédente de l’ACL bloquait tout le trafic TCP vers 192.168.30.x.

Résolution :

* J’ai limité l’ACL uniquement au **port 22** (SSH).
* J’ai ajouté `permit ip any any` pour ne rien casser.
* Nouveaux tests → l’intranet .100 fonctionne de nouveau.

---

### **7.2. ACL appliquée sur la mauvaise interface / mauvais sens**

Au début, j’avais appliqué l’ACL en `out` ou sur une interface non concernée.

Résultat :

* L’ACL ne bloquait strictement rien.

Solution :

* Vérification avec `show ip interface g0/0`
* Application correcte en :

```bash
ip access-group 130 in
```

---

### **7.3. IT lui-même ne pouvait plus faire SSH**

Cause :

* Le `deny tcp any host 192.168.30.200 eq 22` était placé avant la règle `permit` IT.

Résolution :

* Réorganisation de l’ACL :

  * d’abord le **permit** IT,
  * ensuite le **deny** générique.

---

### **7.4. Tests obligatoires avec .100 et .200**

J’ai dû refaire **toute une série de tests**, notamment :

* Ping .100 → vérifier que le serveur reste accessible
* Ping .200 → vérifier les effets ICMP
* SSH .200 → vérifier l’accès autorisé / bloqué

Ces tests m’ont permis d’identifier les lignes incorrectes et d’ajuster l’ACL précisément.

---

## **8. Résultats obtenus**

* SSH activé correctement sur RouterSSH.
* Accès administratif strictement réservé au VLAN IT.
* Direction & RH totalement bloqués en SSH.
* Les services internes (DNS / HTTP / ping .100) restent fonctionnels.
* L’ACL est contrôlée, propre, et ne perturbe pas le reste du réseau.

Ce projet m’a permis d’améliorer ma compréhension :

* de la sécurité d’accès aux équipements Cisco,
* de la logique des ACL avancées,
* de la gestion fine des flux dans un réseau professionnel.

---

## **9. Fichiers du projet**

```
Projet7-SSH-ACL/
├── captures/
│   ├── ACL_Applied_On_G0_0.jpg
│   ├── ACL_SSH_Only_IT.jpg
│   ├── Capture_ACL_Config.jpg
│   ├── HTTP_Direction_OK.jpg
│   ├── PC_Direction_SSH_Blocked.jpg
│   ├── PC_RH_SSH_Blocked.jpg
│   ├── PC_IT_SSH_OK.jpg
│   ├── Ping_Direction_OK.jpg
│   ├── Ping_RH_Blocked.jpg
│   ├── Switch_Config_FA0_5_VLAN30.jpg
│   ├── RouterSSH_IP_Config.jpg
│   └── RouterSSH_SSH_Config.jpg
│
└── projet7_ssh_acl.pkt
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

