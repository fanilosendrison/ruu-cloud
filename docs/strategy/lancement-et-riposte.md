# Gates de lancement et riposte concurrentielle J+1

[Retour à l'index](README.md)

## Un lancement ne doit pas dépendre d'un monopole fonctionnel

Le lancement doit pouvoir réussir commercialement malgré une alternative native, gratuite pour la base installée et suffisamment bonne, annoncée à J+1 — ou déjà présente à J0. L'effet de surprise n'est qu'une accélération temporaire. Le coût d'opportunité de la confidentialité doit être réévalué périodiquement.

## Gates indépendants

| Gate | Conditions attendues | Ne suffit pas |
| --- | --- | --- |
| **T — technique** | Capacité réellement implémentée sur la version livrée, installation sûre, scénarios négatifs et récupération testés, limites documentées | Product Intent, démonstration montée, promesse de fiabilité |
| **A — adoption Core** | Testeurs externes ayant activé et réutilisé Ruu, retours observés, objections prises en compte | Inscriptions, stars, lettres d'intérêt |
| **C — commercial Cloud** | Besoin multi-hôtes prouvé, acheteur, pilotes concrets, valeur payante testée face au natif | Intérêt verbal ou popularité de Core |
| **D — distribution** | Canaux avec actions convenues, matériel autorisé, attribution mesurable, capacité support | Audience théorique ou partenariats non confirmés |

Statut de chaque gate : **PASS, FAIL ou UNKNOWN** avec date, versions, preuves, responsable et décision. Aucune moyenne n'efface un FAIL technique. Les tests techniques restent gouvernés par les procédures Ruu/Cloud, pas par la stratégie commerciale.

## Matrice de publication

- **Core pas sûr** : pas de sortie déclarée stable ; limiter les essais à des environnements isolés et autorisés.
- **Core prêt, Cloud pas prêt** : comparer la croissance organique OSS perdue au bénéfice du secret ; une publication de Core avant Cloud reste envisageable.
- **Core et Cloud prêts** : envisager activation coordonnée si support, facturation, sécurité, partenaires et acquisition sont prêts.
- **Cloud sans preuve d'achat** : ne pas dire que la monétisation est validée ; continuer les pilotes et tests de valeur.
- **Fuite ou intégration native avant J0** : requalifier immédiatement les segments et le calendrier, sans prétendre que la surprise existe encore.

Le seuil numérique de partenaires n'est pas un gate absolu. La rapidité commerciale doit rester subordonnée aux exigences de sûreté.

## Préparer J0

Version et provenance ; documentation honnête ; licence OSS choisie ; distribution Core ; site, installation et support ; offre Cloud réellement disponible si annoncée ; possibilité de traiter un afflux ; signataires/contrats ; autorisations de logos/témoignages ; messages et canaux sous embargo ; protocole J+1 répété.

## Deux veilles distinctes

La [veille technique de Ruu](https://github.com/fanilosendrison/ruu/tree/main/docs/research/competitive-watch) examine les garanties selon ses axes et preuves propres. La **veille de substitution commerciale** mesure : fonctionnalités effectivement disponibles versus annoncées, bundling, tarifs, canaux de distribution, adoption du concurrent, objections, retards de vente, résiliations et achats. Un concurrent techniquement partiel peut être commercialement décisif.

La simple présence des fichiers de veille ne prouve pas qu'un rapport a été produit ni qu'un scheduler est actif. Conserver des observations datées et corrections en historique, et ne jamais transformer le silence public en « capacité absente ».

## Incident concurrentiel : protocole pré-écrit

| Horizon | Travail exigé |
| --- | --- |
| **0–24 h** | Enregistrer annonce, source, date, disponibilité, régions, prix, périmètre, clients exposés ; classifier réel versus annoncé |
| **24–72 h** | Faire des comparaisons reproductibles quand possible, noter résultats positifs/négatifs/inconnus, mobiliser la veille technique |
| **J+3 à J+7** | Parler aux prospects/pilotes ; mesurer choix, annulations, retards, recours à l'alternative et achats réels |
| **J+7 à J+30** | Réorienter canaux, messages, prix ou segment selon preuves ; allouer ressources à la rétention et aux clients défendables |
| **Après J+30** | Bilan daté, corrections d'hypothèses, trajectoire commerciale et décision de poursuivre/pivoter/arrêter une offre |

Ne pas dénigrer ni inventer des lacunes concurrentes. Ne pas vouloir « répondre fonction pour fonction » si les clients préfèrent une approche réellement différente.

## Priorité commerciale d'un événement

**WATCH** (signaux sans impact prouvé), **INVESTIGATE** (substitut accessible à examiner), **RESPOND** (effet observé sur acquisition/prix/rétention), **PIVOT_REVIEW** (segment ou économie invalidés). Ces niveaux sont commerciaux et ne modifient jamais les notes de capacité de la veille technique.

## Divulgation avant le lancement

Identifier ce qui a fuité, traiter les accès, préserver les preuves, informer les personnes concernées si nécessaire, revoir l'embargo et la date, préparer une réponse factuelle. Une copie locale ne disparaît pas après révocation d'un accès GitHub.

## Simulation impérative avant J0

« GitLab livre demain une fonction native gratuite couvrant l'essentiel des besoins ordinaires. Quels clients continueraient à payer Ruu Cloud, pourquoi, à quelle valeur vérifiée ? Quels canaux restent productifs ? »

Si la seule réponse est « Ruu est techniquement meilleur » ou « nous avions 50 partenaires intéressés », le plan commercial n'est pas résistant au scénario J+1.
