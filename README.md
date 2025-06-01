# Pokémon Quiz

## 📖 Description

Pokémon Quiz est une application web interactive développée avec Vue.js qui permet aux joueurs de tester leurs connaissances sur les Pokémon. Le but du jeu est de retrouver de mémoire les noms des Pokémon dans un temps limité.

## ✨ Fonctionnalités

- **Sélection par région** : Choisissez parmi les différentes régions du monde Pokémon (Kanto, Johto, Hoenn, etc.)
- **Différents niveaux de difficulté** :
  - **Facile** : 20 minutes pour trouver tous les Pokémon
  - **Moyen** : 15 minutes
  - **Difficile** : 10 minutes
  - **Entraînement** : Mode sans limite de temps
- **Mode Extrême** : Trouvez tous les Pokémon de toutes les générations
  - **Entraînement** : Sans limite de temps
  - **Hardcore** : 60 minutes pour trouver plus de 900 Pokémon!
- **Visualisation des résultats** : Les Pokémon trouvés apparaissent avec leur sprite officiel
- **Suivi de progression** : Une barre de progression affiche votre avancement en temps réel
- **Timer visuel** : Indication du temps restant avec des alertes visuelles

## 🛠️ Technologies utilisées

- **Vue.js 3** avec la Composition API
- **TypeScript** pour le typage statique
- **Vite** comme outil de build
- **CSS** personnalisé pour l'interface

## 📦 Structure du projet

```
src/
├── App.vue                # Composant racine
├── main.ts               # Point d'entrée de l'application
├── assets/
│   └── pokemon-images/   # Images des Pokémon
├── components/
│   ├── ConfirmationModal.vue  # Modal de confirmation
│   ├── GameBoard.vue          # Plateau de jeu principal
│   ├── PokemonImage.vue       # Affichage d'un Pokémon
│   ├── RegionSelector.vue     # Sélecteur de région
│   ├── Timer.vue              # Composant de minuterie
│   └── VictoryModal.vue       # Modal de victoire
├── data/
│   └── pokemon.ts            # Données des Pokémon
└── styles/
    └── main.css             # Styles globaux
```

## 🚀 Installation et démarrage

### Prérequis

- Node.js (v16 ou supérieur)
- npm ou yarn

### Installation

```bash
# Cloner le dépôt
git clone https://github.com/yourusername/poke-quiz.git
cd poke-quiz

# Installer les dépendances
npm install
```

### Lancement en développement

```bash
npm run dev
```

### Construction pour la production

```bash
npm run build
```

## 🎮 Comment jouer

1. Sélectionnez une région (ou le mode Extrême pour toutes les régions)
2. Choisissez un niveau de difficulté
3. Cliquez sur "Commencer"
4. Tapez les noms des Pokémon dans la barre de recherche
5. Appuyez sur Entrée ou cliquez sur "Valider" après chaque nom
6. Trouvez tous les Pokémon avant la fin du temps pour gagner!

## 📝 Notes

- Les noms des Pokémon sont en français
- Le jeu est sensible aux accents et aux caractères spéciaux
- En mode entraînement, il n'y a pas de limite de temps, parfait pour apprendre les noms

---

Amusez-vous bien et attrapez-les tous!
