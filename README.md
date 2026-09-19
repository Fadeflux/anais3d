# Anais3D — atelier d'impression 3D

Application web installable sur PC et téléphone : bobines de filament, coût de revient, productions, prints ratés, stock de pièces, commandes clients, ventes, bilan du mois en PDF et étiquettes QR des bobines. Les données sont enregistrées dans **Supabase** (PostgreSQL) et restent utilisables **sans réseau** : les actions attendent sur l'appareil et partent toutes seules au retour de la connexion.

C'est la même application que Paulo3D, pour un autre atelier : **base de données séparée** (projet Supabase à part), nom, logo et noms de stockage propres. Les deux sites peuvent être ouverts sur le même appareil sans jamais se mélanger.

## Contenu du dossier

| Dossier / fichier | Rôle |
|---|---|
| `docs/` | **Le site construit** : `index.html`, `sw.js` (hors-ligne), `manifest.webmanifest`, `icons/` (servi par Railway) |
| `supabase/schema.sql` | **Le script à exécuter dans le projet Supabase d'Anais3D** (tables, calculs, sécurité, temps réel) |
| `.github/workflows/veille-supabase.yml` | Signe de vie automatique : empêche la mise en pause du projet Supabase gratuit |

Le code source est commun avec Paulo3D (dossier `Paulo3D`, `src/`). Une mise à jour d'Anais3D se construit depuis ce dossier : `node tools/build.mjs --site anais3d` (le site arrive dans `Anais3D/docs/`), puis on pousse ce dépôt.

---

## 1. Créer la base Supabase d'Anais3D (≈ 10 minutes)

1. Sur [supabase.com](https://supabase.com), crée un **nouveau projet** gratuit (région Europe), par exemple « anais3d ». Garde le mot de passe de la base en lieu sûr.
2. **SQL Editor** → **New query** → colle **tout** le fichier `supabase/schema.sql` → **Run**.
   Vérification : `select public.p3d_version();` doit répondre `3`.
3. Bouton **Connect** (ou **Project Settings → API Keys**) : note l'**URL du projet** (`https://xxxx.supabase.co`) et la clé **publishable**. ⚠️ Jamais la clé **secret** / **service_role**.
4. **Authentication → URL Configuration** : `https://fadeflux.github.io/anais3d/` dans **Site URL** et **Redirect URLs**.
5. **Fermer les inscriptions** : **Authentication → Sign In / Providers → Allow new users to sign up** = désactivé ; **Minimum password length** = `12`, **Password requirements** = « Lowercase, uppercase letters, digits and symbols » ; **Authentication → Rate Limits → sign-ups and sign-ins** = `10`.
6. **Créer LE compte d'Anaïs** : **Authentication → Users → Add user → Create new user** (email + mot de passe d'au moins 12 caractères avec minuscule, majuscule, chiffre et symbole), coche **Auto Confirm User**.
7. **Authentication → Multi-Factor** : **TOTP** activé (double authentification).

## 2. Le site en ligne (Railway)

1. Le site est servi par GitHub Pages (dossier `docs/`) : **https://fadeflux.github.io/anais3d/**. L'ancienne adresse `anais3d.up.railway.app` redirige ici.
2. Mise en ligne : pousser ce dépôt (GitHub publie `docs/` en une minute environ).
3. **Anti-pause** : **Settings → Secrets and variables → Actions** → secrets `SUPABASE_URL` et `SUPABASE_KEY` (clé **publishable** du projet d'Anais3D).

## 3. Première utilisation

1. Ouvre le site → colle l'URL et la clé publique → **Tester et continuer** → connecte-toi avec le compte d'Anaïs.
2. Installer sur téléphone : iPhone → Safari → **Partager** → **Sur l'écran d'accueil** ; Android → Chrome → menu ⋮ → **Installer l'application**.
3. **Paramètres** : taux machine et main-d'œuvre, machines, commissions des canaux de vente, et **Double authentification** (conseillée).

## Bon à savoir

- **Sauvegardes** : le tableau de bord rappelle tous les 30 jours de télécharger une sauvegarde complète (bouton **Sauvegarder**) : Supabase gratuit n'en garde aucune.
- **Téléphone perdu** (double authentification) : dans le projet Supabase d'Anais3D, **SQL Editor** : `delete from auth.mfa_factors where user_id = (select id from auth.users where email = 'EMAIL-DU-COMPTE');`, puis la réactiver sur le nouveau téléphone.
- **Étiquettes QR** : Bobines → **Étiquettes** → **PDF** (feuille A4 de 14 étiquettes). Dans l'appli, le bouton **Scanner** lit une étiquette et ouvre directement la pesée.
