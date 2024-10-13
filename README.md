# Synthèse de mouvements avec un modèle VDM
Ce projet fait partie de notre travail de fin d'année 2024 et est une implémentation de notre recherche sur la synthèse de mouvements. Il vise à modéliser et générer des séquences de mouvements humains avec un modèle de diffusion. 

## Table des matières
- [Description du unet3D](#description-du-générateur)
- [Résultats expérimentaux](#résultats-expérimentaux)
- [Installation](#installation)
- [Configuration matérielle](#configuration-matérielle)

## Description du unet3D

Le **Unet3D** est utilisé pour inverser le processus de diffusion, en apprenant à retirer progressivement le bruit d'une vidéo bruitée afin de restaurer la vidéo originale ou générer une nouvelle vidéo. 

À chaque étape, le **Unet3D** prédit le bruit présent dans la vidéo bruitée. Cette prédiction est soustraite de la vidéo actuelle pour produire une version légèrement moins bruitée. Ce processus est répété sur plusieurs étapes (10000 dans le cas de ce modèle), avec une amélioration progressive de la qualité de la vidéo à chaque étape.

Le modèle suit une architecture en U avec des **couches d'encodage et de décodage**. L'encodage réduit progressivement la résolution de la vidéo à travers des convolutions 3D pour capturer les caractéristiques globales. Au niveau du **bottleneck**, la vidéo est représentée sous une forme abstraite. Ensuite, le décodage utilise des convolutions transposées pour augmenter la résolution tout en fusionnant les informations avec celles capturées lors de l'encodage via les **skip connections**.

Après la dernière étape de diffusion inverse, tout le bruit est retiré et la vidéo générée est prête, conforme aux conditions d'entrée et aux relations temporelles.

![Figure du Unet3D](https://github.com/mmeryemelm/VDM/blob/main/UNET3D.JPG)


## Résultats expérimentaux
Nous avons utilisé un modèle de diffusion (VDM) qui s'inspire des travaux de Ho et al. (2020). Nous avons adapté leur modèle pour mieux convenir à la tâche de synthèse de mouvements humains, en entraînant le  modèle sur un large ensemble de données de gestes humains de la base de données NATOPS.

Nous avons utilisé une fonction de perte basée sur la distance euclidienne entre les séquences générées et les séquences réelles ainsi qu'une perte VGG pour optimiser le modèle.

![Résultats expérimentaux](https://github.com/mmeryemelm/VDM/blob/main/samples/foldwings.jpeg)


## Installation

Pour exécuter ce projet, assurez-vous d'installer les bibliothèques suivantes en exécutant la commande :

```bash
pip install -r requirements.txt
```



## Configuration de la base de données

### Étape 1: Téléchargement de la base de données Natops

1. **Clonage du dépôt GitHub :**
   - Ouvrez votre terminal.
   - Entrez la commande :
     ```bash
     git clone https://github.com/yalesong/natops.git
     ```
   - Changez de répertoire avec :
     ```bash
     cd natops
     ```

2. **Exécution du script de téléchargement :**
   - Assurez-vous que les outils `wget` et `unzip` sont installés sur votre machine.
   - Lancez le script :
     ```bash
     ./download_natops.sh
     ```
---

### Étape 2: Prétraitement des vidéos

#### 1. Récupération des vidéos AVI
Ce script récupère uniquement les fichiers vidéo AVI de la base de données Natops. 

#### 2. Segmentation des vidéos
Ce script segmente les vidéos en utilisant le fichier `segmentation.txt` pour récupérer toutes les répétitions d'une action. 



## Configuration matérielle

Ce projet a été développé et testé sur une machine équipée d'un GPU NVIDIA GeForce RTX 3060 avec 6 Go de mémoire. Le GPU supporte CUDA 12.5 et dispose d'une capacité de calcul de 7.8 TFLOPS.
