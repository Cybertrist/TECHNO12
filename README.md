# TECHNO12 Records

Page vitrine d'un label techno fictif, sous la forme d'un concert immersif de type warehouse rave.

Tout tient dans **un seul fichier `index.html`** (HTML, CSS et JS intégrés). Il n'y a aucune dépendance, aucun CDN, aucune police, aucune image et aucun fichier audio. La page fonctionne donc hors Internet.

## Contenu de la page

- **Scène animée en Canvas 2D** : un DJ en silhouette derrière ses platines et sa table de mixage, un mur LED qui réagit au spectre audio, des lasers en éventail (cyan, magenta, vert, jaune) calés sur le tempo, des lyres, de la fumée volumétrique, des machines à fumée/CO2, des lance-flammes, des flashs et une foule qui saute bras levés.
- **Titre « TECHNO12 »** avec effet glitch et dégradé néon animé, sous-titre « Live • Warehouse Session • 2026 ».
- **Bouton « Drop the beat »** : si le navigateur est connecté à Spotify, il lance la playlist « This Is SICK LEGEND » via le Web Playback SDK (aucun lecteur visible, juste « titre · Spotify » à côté du BPM). Sinon, il lance une boucle hardstyle originale à 150 BPM générée en Web Audio. Spotify ne donne pas accès au signal audio : pendant la lecture Spotify, la scène suit un tempo approximatif.
- **Page cachée `?connexion`** (ex. `https://cybertrist.github.io/TECHNO12/?connexion`) : bouton « Se connecter avec Spotify » (OAuth PKCE, aucun secret dans le code). Chaque visiteur utilise son propre compte **Premium** ; les jetons restent dans le navigateur. Ne fonctionne que sur ordinateur.

  Configuration : créer une app sur developer.spotify.com, cocher « Web Playback SDK », ajouter les adresses de retour `https://cybertrist.github.io/TECHNO12/` (et `http://127.0.0.1:8712/` pour tester en local), puis renseigner le Client ID dans `SPOTIFY_CLIENT_ID` (index.html) ou directement sur la page `?connexion`. En mode développement, seuls les comptes ajoutés dans l app (User Management) peuvent se connecter.
- **Logo** : intégré directement dans `index.html` (en-tête, pied de page, icône d'onglet), sans fichier image séparé.
- **Égaliseur animé** branché sur un `AnalyserNode`.
- **En-tête** avec le logo et le menu (Line-up, Releases, Tickets), puis les sections correspondantes.

## Accessibilité et performances

- Le son ne démarre qu'au clic, jamais automatiquement.
- `prefers-reduced-motion` est respecté : pas de stroboscope ni de glitch, des mouvements ralentis et des flashs fortement atténués.
- Le bouton « Flashs ON/OFF » de l'en-tête coupe les flashs plein écran. Même activés, ils sont limités à environ 2 par seconde, sous le seuil de 3 flashs/s.
- La page vise 60 fps : décors pré-rendus hors écran, gradients mis en cache, densité de pixels plafonnée. La résolution baisse toute seule si la machine peine, et l'animation passe à 30 fps quand la scène n'est plus visible.
- Le son se met en pause quand l'onglet est masqué.
- La page est responsive, du desktop au mobile.

## Tester en local

Ouvrez `index.html` directement dans un navigateur, ou servez le dossier :

```bash
python3 -m http.server 8000
# puis http://localhost:8000
```

## Déploiement sur Apache

```bash
# 1. Copier le site
sudo mkdir -p /var/www/techno12
sudo cp index.html /var/www/techno12/
sudo chown -R www-data:www-data /var/www/techno12
sudo chmod 644 /var/www/techno12/index.html

# 2. Déclarer le site
sudo tee /etc/apache2/sites-available/techno12.conf > /dev/null <<'EOF'
<VirtualHost *:80>
    ServerName techno12.local
    DocumentRoot /var/www/techno12
    <Directory /var/www/techno12>
        Options -Indexes
        AllowOverride None
        Require all granted
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/techno12_error.log
    CustomLog ${APACHE_LOG_DIR}/techno12_access.log combined
</VirtualHost>
EOF

# 3. Activer et recharger
sudo a2ensite techno12.conf
sudo apache2ctl configtest && sudo systemctl reload apache2
```

Remplacez `techno12.local` par le nom DNS interne voulu, ou ajoutez-le au fichier `hosts` des postes clients. Sur RHEL/CentOS, le service s'appelle `httpd`, la configuration va dans `/etc/httpd/conf.d/techno12.conf`, et l'utilisateur est `apache`.

---

Site réalisé par Tristan Joncour · ENSIBS CyberDef5
