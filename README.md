## Créer le projet 
npx create-react-app <nom de votre projet>

## Github actions générer le main.yml de base
aller dans votre git puis dans l'onglet actions et suivez les instrutions

## Modifier le .yml pour votre CI CD
### Nommer votre ci cd 
name: <NOM>
### Nommer votre ci cd 
on:
  push:
    branches: [ <Nom de votre branche par défaut: master> ]

### Build votre projet react sur un ubuntu et installer les dépendances
    runs-on: ubuntu-latest
    steps:
      - name: Clone repository
        uses: actions/checkout@v2

      - name: Use Node.js 16.x
        uses: actions/setup-node@v1
        with:
          node-version: 16.x
      - name: Install dependencies
        run: npm install

### Aller dans le dossier artefact et lancer la commande gradlew assembleRelease et on lui donne les droits d'écriture
      - name: Build Artefact 
        run: cd android && chmod +x gradlew && ./gradlew assembleRelease

### Installer le paquet java
      - uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'
          cache: 'gradle'
### Executer les tests de base de votre projet (déjà inclut à la création de l'app)
- name: Run tests 
        run: npm run test

### variables secret
Dans les settings de votre projet dans git, il vous faut rentrer les variables d'environnement du votre serveur
ainsi que la clé privée de votre poste

### Ajout de la variable ssh privée
      - name: ajout Known Hosts
        run: ssh-keyscan -H ${{ secrets.HOST }} >> ~/.ssh/known_hosts

### On copie dans dossier build avec rsync en ssh avec notre login du serveur distant et l'ip de ce serveur dans le dossier /web/build
   - name: run deploy
        run: rsync -avz .\android\app\build\outputs\apk\release\app-release.apk ${{ secrets.USERNAME }}@${{ secrets.HOST }}:~/mobile/

https://github.com/Cody-Pronnier/reactnative
https://github.com/Cody-Pronnier/nfschoolreact