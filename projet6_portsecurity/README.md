# **Projet 6 : Sécurisation du Switch (Port Security & Storm Control)**

## **1. Description**

Ce projet s’inscrit dans le cadre de mon challenge **“30 Cisco Projects”**.
L’objectif est de sécuriser un switch Cisco face aux menaces courantes sur le réseau local, notamment :

* l’usurpation d’adresse MAC,
* le branchement non autorisé d’un équipement,
* les attaques par flood ou les tempêtes de broadcast.

Pour atteindre ces objectifs, j’ai mis en œuvre deux mécanismes :

* **Port Security** : limitation et apprentissage des adresses MAC autorisées, blocage automatique en cas de violation.
* **Storm Control** : protection contre les surcharges de trafic (broadcast / multicast).

Ce projet me permet de renforcer mes bases en **sécurité réseau**, en détectant et en bloquant les comportements anormaux directement au niveau du switch.

---

## **2. Objectifs techniques**

* Activer Port Security sur les ports utilisateurs.
* Limiter le nombre de MAC autorisées par port.
* Apprendre automatiquement la MAC du poste connecté (sticky).
* Déclencher un blocage automatique en cas de violation.
* Simuler une attaque via modification de MAC.
* Configurer Storm Control pour limiter les tempêtes réseau.
* Vérifier l’état des ports et les violations détectées.

---

## **3. Architecture utilisée**

Ce projet réutilise la topologie du projet précédent :

* **Switch Cisco 2960**
* **PC Direction (Fa0/1)**
* **PC RH (Fa0/2)**
* **PC IT (Fa0/3)**
* **Serveur DNS/Web (Fa0/4)**
* **Routeur Cisco 2911 (Router-on-a-Stick)**

Aucune modification du câblage n’a été nécessaire.

---

## **4. Étapes de configuration**

### **4.1. Activation de Port Security sur Fa0/1**

```bash
Switch(config)# interface fa0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation shutdown
Switch(config-if)# switchport port-security mac-address sticky
```

Cette configuration :

* autorise un seul appareil sur ce port,
* apprend automatiquement la MAC du PC Direction,
* bloque automatiquement le port en cas de violation.

### **4.2. Vérification de l’apprentissage MAC**

Commande :

```
show port-security interface fa0/1
```

Résultat attendu :

* Status : secure-up
* Sticky MAC : 1
* Aucune violation enregistrée

📸 Capture : *Show_PortSecurity_FA0_1.jpg*

---

### **4.3. Simulation d’une violation (MAC spoofing)**

Pour simuler un intrus :

* Modification de la MAC sur le PC Direction.
* Génération de trafic (ping).

Résultat attendu :

* Le switch détecte une MAC inconnue.
* Le port passe en **secure-shutdown**.

📸 Captures :

* *PC_Violation_Ping_Fail.jpg*
* *Switch_Violation_SecureShutdown.jpg*

---

### **4.4. Réactivation du port**

Commande :

```bash
interface fa0/1
shutdown
no shutdown
```

📸 Capture : *Switch_Port_Up_After_Reset.jpg*

---

### **4.5. Storm Control**

Ajout d’une protection contre les tempêtes de broadcast :

```bash
Switch(config)# interface range fa0/1 - 3
Switch(config-if-range)# storm-control broadcast level 50 80
Switch(config-if-range)# storm-control multicast level 50 80
Switch(config-if-range)# storm-control action shutdown
```

---

## **5. Résultats obtenus**

* Port Security opérationnel avec apprentissage automatique.
* Détection et blocage immédiat d’une adresse MAC non autorisée.
* Port remis en service manuellement après la violation.
* Protection contre les tempêtes réseau activée.
* Tests concluants avec captures complètes.

Ce projet démontre ma capacité à sécuriser un switch Cisco face à des attaques locales tout en maintenant un environnement réseau stable.

---

## **6. Fichiers du projet**

```
Projet6-PortSecurity/
│
├── captures/
│   ├── PC_Violation_Ping_Fail.jpg
│   ├── Show_PortSecurity_FA0_1.jpg
│   ├── Switch_Violation_SecureShutdown.jpg
│   └── Switch_Port_Up_After_Reset.jpg
│
└── projet6_portsecurity.pkt
```

---

## **7. Auteur**

**Dylan CHRIIST BEBEY NZEKE**
Bachelor 3 – Cybersécurité & Réseaux (ECE Paris)
Paris, France
GitHub : [https://github.com/DylanBebey](https://github.com/DylanBebey)
LinkedIn : [https://linkedin.com/in/dylan-bebey-012886330](https://linkedin.com/in/dylan-bebey-012886330)

---
