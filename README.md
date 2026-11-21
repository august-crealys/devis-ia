# Devis IA

Devis IA est une application web de gestion commerciale pour les petites
entreprises. Elle permet de gérer les clients, les produits, les fournisseurs,
les devis, les factures et les paiements, avec une interface en français et des
montants affichés en FCFA.

## Fonctionnalités

- Créer et suivre des devis et des factures, puis les imprimer ou les exporter
  en PDF. Plusieurs modèles de document sont disponibles.
- Gérer les clients, les produits, les fournisseurs et les créances, y compris
  les paiements partiels.
- Consulter un tableau de bord avec les principaux indicateurs de l'entreprise.
- Préparer un devis ou une facture à partir d'une description libre ou d'une
  image. L'extraction utilise les fournisseurs IA configurés ; un OCR français
  exécuté dans le navigateur est également disponible.
- Se connecter par e-mail/mot de passe ou avec Google (via Supabase Auth).
- Continuer à consulter et modifier certaines données après une première
  connexion en ligne. Les opérations prises en charge sont mises en attente et
  synchronisées au retour du réseau.
- Gérer les essais et abonnements. Le paiement Orange Money est disponible si
  ses identifiants serveur sont configurés.

Le mode hors ligne ne couvre pas toutes les opérations : la connexion, les
appels IA, les paiements et certaines opérations nécessitant une validation
serveur demandent une connexion Internet.

## Technologies

- Next.js 16, React 19 et TypeScript
- Supabase : PostgreSQL, authentification et stockage
- Tailwind CSS
- Vitest pour les tests unitaires et Playwright pour les tests end-to-end

## Prérequis

- Node.js 20.9 ou une version ultérieure
- npm
- Un projet Supabase configuré pour le développement

## Cloner et démarrer

```bash
git clone https://github.com/august-crealys/devis-ia.git
cd devis-ia
npm ci
```

Créez un fichier `.env.local` à la racine du projet. Renseignez au minimum les
identifiants de votre environnement Supabase :

```env
NEXT_PUBLIC_SUPABASE_URL=https://<project-ref>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=<supabase-anon-key>
NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

Pour utiliser la génération assistée par IA, ajoutez au moins une clé de
fournisseur prise en charge :

```env
GROQ_API_KEY=
GEMINI_API_KEY=
MISTRAL_API_KEY=
OPENROUTER_API_KEY=
OPENAI_API_KEY=
```

`GOOGLE_GENERATIVE_AI_API_KEY` est également accepté comme alias de
`GEMINI_API_KEY`. Les clés IA sont utilisées côté serveur ; ne leur ajoutez
jamais le préfixe `NEXT_PUBLIC_`. Sans clé IA, les fonctionnalités manuelles
restent disponibles, mais l'extraction assistée par IA ne l'est pas.

La clé `SUPABASE_SERVICE_ROLE_KEY` n'est nécessaire que pour certaines
opérations privilégiées côté serveur. Ne la communiquez jamais au navigateur et
ne la committez jamais. `REDIS_URL`, les clés Orange Money, `CRON_SECRET` et les
variables `ADMIN_*` ne sont nécessaires que pour les fonctions correspondantes.

Le fichier `.env.local` est ignoré par Git. Demandez les accès et les valeurs de
configuration de développement au responsable du projet ; ne réutilisez pas de
clés de production.

### Base Supabase

Le schéma de base se trouve dans [`supabase/schema.sql`](supabase/schema.sql) et
les évolutions dans [`supabase/migrations/`](supabase/migrations/). Pour un
nouvel environnement, utilisez une base de développement et appliquez le schéma
ainsi que les migrations nécessaires à la version du projet. Certaines
migrations dépendent d'un schéma déjà existant : vérifiez leur contenu et leur
ordre avec le responsable de la base avant de les appliquer. Ne lancez pas de
migration locale directement sur une base de production.

Pour l'authentification Google, activez le fournisseur dans Supabase Auth et
configurez ses URL de redirection pour l'environnement local
(`http://localhost:3000/auth/callback`) et pour votre fournisseur OAuth.

## Lancer l'application

```bash
npm run dev
```

Ouvrez <http://localhost:3000>. Le script de développement prépare aussi les
ressources OCR locales.

## Travailler sur le projet

Créez une branche pour chaque changement :

```bash
git switch -c feat/description-courte
```

Après vos modifications, lancez les vérifications adaptées :

```bash
npm run typecheck
npm run lint
npm run test:unit
```

Avant une pull request, la vérification complète est disponible avec :

```bash
npm run check
```

Elle exécute le contrôle TypeScript, ESLint, Prettier, les tests unitaires et le
build de production. Les tests end-to-end se lancent séparément avec
`npm run test:e2e`.

Poussez votre branche puis ouvrez une pull request vers `main` :

```bash
git add .
git commit -m "Décrire le changement"
git push -u origin feat/description-courte
```

Si vous n'avez pas les droits d'écriture sur le dépôt, travaillez depuis un
fork et proposez votre pull request depuis celui-ci.

## Organisation du code

- `src/app/` : pages, routes API et actions serveur
- `src/components/` : composants de l'interface
- `src/lib/` : accès aux services, règles métier et fonctions partagées
- `src/lib/__tests__/` : tests unitaires
- `supabase/` : schéma initial et migrations SQL
- `e2e/` : tests Playwright

## Autres commandes

```bash
npm run build       # Construire l'application
npm run start       # Servir le build de production localement
npm run test:e2e    # Lancer les tests end-to-end
```
