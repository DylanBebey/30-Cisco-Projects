# 🌐 Projet 1 — LAN d’entreprise sous Cisco Packet Tracer  
_Status : Terminé ✅ • Dossier : `Projet1-LAN-Cisco/` • Fichier PT : `lan_base.pkt`_

---

## 1️⃣ Introduction
Ce projet inaugure mon **30 Cisco Projects Challenge**, un parcours personnel que j’ai lancé pour renforcer mes compétences en **réseaux Cisco** et en **cybersécurité**.  
L’objectif de ce premier projet est de construire un **réseau local (LAN)** d’entreprise minimaliste mais fonctionnel, avec **adressage IP statique**, **connectivité validée**, et **documentation complète**.

---

## 2️⃣ Objectifs
- Créer une **topologie LAN** (3 postes + 1 switch).
- Configurer les **adresses IP statiques** de chaque poste.
- Tester la **connectivité ICMP (ping)** entre les hôtes.
- Vérifier la **table MAC** du switch.
- Documenter le tout avec **captures, rapport et explications.**

---

## 3️⃣ Pourquoi ce projet est important
Ce premier projet pose les **bases fondamentales** du métier d’administrateur réseau :
- Comprendre la **couche 2 (Switching)** et la communication **MAC/IP**.
- Maîtriser les outils de diagnostic comme le **ping** et la **table MAC**.
- Apprendre à **documenter un projet technique** comme en entreprise.  
C’est également une **première brique essentielle en cybersécurité**, car on ne peut pas sécuriser ce qu’on ne comprend pas.

---

## 4️⃣ Topologie & Plan d’adressage

```

PC1 (Poste_RH)  ─┐
PC2 (Poste_IT)  ──┤── Switch Cisco 2960
PC3 (Poste_DIR) ─┘

```

| Hôte | Nom | Adresse IP | Masque | Passerelle |
|------|-----|-------------|---------|-------------|
| PC1 | Poste_RH | 192.168.10.1 | 255.255.255.0 | (vide) |
| PC2 | Poste_IT | 192.168.10.2 | 255.255.255.0 | (vide) |
| PC3 | Poste_DIR | 192.168.10.3 | 255.255.255.0 | (vide) |
| Switch | Switch_LAN | — | — | — |

> 🔹 Pas de passerelle ici : un seul réseau sans routage.

---

## 5️⃣ Prérequis
- Notions : IP/mask, ping, interface PC/switch.
- Savoir manipuler les équipements dans **Cisco Packet Tracer**.

---

## 6️⃣ Outils utilisés
- **Cisco Packet Tracer** 8.2+
- **Switch Cisco 2960**
- **3 PC**
- Système d’exploitation hôte : Windows / Linux

---

## 7️⃣ Organisation du dépôt

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
├── docs/
│   └── rapport_projet1.pdf
├── lan_base.pkt
└── README.md

```

---

## 8️⃣ Définitions rapides
- **LAN (Local Area Network)** : réseau local, limité à un bâtiment ou service.  
- **Switch (couche 2)** : relie les hôtes selon leurs adresses **MAC**.  
- **ICMP / ping** : protocole permettant de tester la communication entre machines.

---

## 9️⃣ Étapes pratiques

### 🔹 Étape 1 — Créer la topologie
1. Ouvrir **Packet Tracer**.  
2. Ajouter 3 **PC** (End Devices → PC).  
3. Ajouter 1 **Switch 2960**.  
4. Relier chaque PC au switch avec un **câble droit (Copper Straight-Through)**.  
   - PC1 (Fa0) → Switch (Fa0/1)  
   - PC2 (Fa0) → Switch (Fa0/2)  
   - PC3 (Fa0) → Switch (Fa0/3)

📸 Capture : `captures/topologie_lan.png`

---

### 🔹 Étape 2 — Renommer les hôtes
- PC1 → **Poste_RH**
- PC2 → **Poste_IT**
- PC3 → **Poste_DIR**
- Switch → **Switch_LAN**

---

### 🔹 Étape 3 — Configurer les adresses IP
Onglet **Desktop → IP Configuration** sur chaque PC :

| PC | IP | Masque | Passerelle |
|----|----|---------|------------|
| PC1 | 192.168.10.1 | 255.255.255.0 | — |
| PC2 | 192.168.10.2 | 255.255.255.0 | — |
| PC3 | 192.168.10.3 | 255.255.255.0 | — |

📸 Captures :  
- `ip_pc1.png`, `ip_pc2.png`, `ip_pc3.png`

---

### 🔹 Étape 4 — Vérifier les liens
CLI du Switch :
```

Switch> enable
Switch# show interfaces status

```
📸 Capture : `captures/show_interfaces.png`

---

### 🔹 Étape 5 — Tester la connectivité
Depuis **PC1** :
```

ping 192.168.10.2
ping 192.168.10.3

```
Depuis **PC2** :
```

ping 192.168.10.3

```

📸 Captures :  
- `ping_pc1_pc2.png`  
- `ping_pc2_pc3.png`

---

### 🔹 Étape 6 — Vérifier la table MAC
CLI du switch :
```

Switch# show mac address-table

```

📸 Capture : `captures/mac_table.png`

---

## 🔍 Étape 7 — Sauvegarde
**File → Save As →** `lan_base.pkt`

---

## 10️⃣ Tests de validation
- [x] IPs uniques et cohérentes (/24)  
- [x] Liens “connected” (vert)  
- [x] Ping OK dans tous les sens  
- [x] 3 MAC apprises par le switch  

---

## 11️⃣ Dépannage
**Ping échoué ?**
- Vérifie les câbles (verts).  
- Attends quelques secondes après branchement.  
- Vérifie les IP/mask.

**Table MAC vide ?**
- Relance un ping : la table se remplit dynamiquement.

---

## 12️⃣ Livrables
| Type | Fichier / Dossier |
|------|--------------------|
| Packet Tracer | `lan_base.pkt` |
| Captures | `captures/*.png` |
| Rapport PDF | `docs/rapport_projet1.pdf` |
| Notes Cisco | `configurations/notes_cisco.txt` |

---

## 13️⃣ Commandes utilisées
```

Switch> enable
Switch# show interfaces status
Switch# show mac address-table

```

---

## 14️⃣ Résultats attendus
- **Ping réussi** :  
  `Reply from 192.168.10.2: bytes=32 time<1ms TTL=128`
- **Table MAC** : 3 entrées dynamiques (`Fa0/1`, `Fa0/2`, `Fa0/3`)

---

## 15️⃣ Publication sur GitHub
1. Créer le dossier `Projet1-LAN-Cisco/` dans ton dépôt principal `30-Cisco-Projects/`.
2. Ajouter :
   - Ce `README.md`
   - Le fichier `lan_base.pkt`
   - Les dossiers `captures/`, `docs/`, `configurations/`
3. Commit :  
   **"Projet 1 – LAN de base : topologie, IP, pings, captures, rapport."**

---

## 16️⃣ Modèle de rapport PDF
Structure recommandée :
1. Objectif du projet  
2. Schéma de la topologie  
3. Table IP  
4. Étapes & commandes  
5. Résultats des tests  
6. Difficultés rencontrées & solutions  
7. Conclusion & transition vers le Projet 2 (VLANs)

---

## 17️⃣ Auteur
👤 **Nom :** Dylan CHRIIST BEBEY NZEKE  
🎓 **Formation :** Bachelor 3 – Administration d’infrastructure sécurisée (ECE Paris)  
📍 **Localisation :** Paris, France  
📧 **Email :** [dylanchriist@gmail.com](mailto:dylanchriist@gmail.com)  
🔗 **LinkedIn :** [www.linkedin.com/in/dylan-bebey-012886330/](https://www.linkedin.com/in/dylan-bebey-012886330/)  
💻 **GitHub :** [github.com/DylanBebey](https://github.com/DylanBebey)

---

## 18️⃣ Licence
📜 MIT License — libre de réutilisation avec mention de l’auteur.

---

## 19️⃣ Changelog
| Version | Date | Description |
|----------|------|--------------|
| v1.0 | Octobre 2025 | Première version stable (topologie, IP, ping, doc complète) |

---
