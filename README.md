# Dispo-app

Site vitrine statique : une seule page `index.html`, sans build.

## Mise en ligne sur Cloudflare Pages (gratuit)
1. Cloudflare > Workers & Pages > Create > Pages > Connect to Git, choisir ce dépôt.
2. Framework preset : None. Build command : (vide). Build output directory : `/`.
3. Save and Deploy. L'adresse sera `<nom-du-projet>.pages.dev`.

## À régler dans `index.html`
- `WA_NUMBER` (en bas du fichier) : numéro WhatsApp pro, format 32470123456.
- Nom de marque : « Dispo-app » (nav, titre, pied de page).
- Ajouter les mentions légales quand le numéro d'entreprise (BCE) existe.
- À confirmer avant diffusion : hébergement des données dans l'UE, séparation des données par salon, prix TTC ou HTVA.
