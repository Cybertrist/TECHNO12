# TECHNO12 Records

Page vitrine d'un label techno fictif, sous la forme d'un concert immersif de type warehouse rave.

Tout tient dans **un seul fichier `index.html`** (HTML, CSS et JS intégrés). Il n'y a aucune dépendance, aucun CDN, aucune police, aucune image et aucun fichier audio. La page fonctionne donc hors Internet.

## Contenu de la page

- **Scène animée en Canvas 2D** : un DJ en silhouette derrière son booth, un mur LED qui réagit au spectre audio, des lasers en éventail (cyan, magenta, vert, jaune) calés sur le tempo, des lyres, de la fumée, des jets de CO2 au drop, des flashs et une foule qui saute bras levés.
- **Titre « TECHNO12 »** avec effet glitch et dégradé néon animé, sous-titre « Live • Warehouse Session • 2026 ».
- **Bouton « Drop the beat »** : lance une boucle techno à 128 BPM synthétisée en temps réel avec la Web Audio API (kick, hi-hats, clap, basse acide de type 303, nappe et montée pendant le breakdown). L'arrangement tourne sur 32 mesures (drop, breakdown, build) et les visuels suivent l'horloge audio.
- **Égaliseur animé** branché sur un `AnalyserNode`.
- **En-tête** avec le logo et le menu (Line-up, Releases, Tickets), puis les sections correspondantes. Le pied de page affiche le crédit.

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
