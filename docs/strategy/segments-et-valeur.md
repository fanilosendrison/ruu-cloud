# Segments, personas et avantage achetable

[Retour à l'index](README.md)

## Frontière Core / Cloud

**Core OSS :** coordination versionnelle fondée sur Git pour un hôte ; multiples sessions et agents, plusieurs dépôts, chevauchements, état exact, checkpoints, convergence sûre, récupération et inspection suivant les autorités normatives. Le produit local doit être **complet**, sans garanties essentielles délibérément retenues.

**Cloud :** domaine de coordination étendu aux équipes opérant depuis plusieurs hôtes indépendants, exploité comme service, même modèle de vérité mécanique. Cloud ne doit pas devenir un prétexte pour faire mal fonctionner Core.

Promesse simple à tester : **« Tu gères l'intention de développement ; Ruu gère les versions. »** Ne jamais présenter une capacité projetée comme déjà expédiée.

## Segments de départ (hypothèses)

| Segment | Entrée | Question commerciale |
| --- | --- | --- |
| Développeurs utilisant plusieurs coding agents simultanément | Core privé puis OSS | Peuvent-ils déléguer la gestion des branches et de l'état stale ? |
| Équipes de 5 à 30 développeurs, plusieurs machines | Core puis Cloud | Paieront-elles pour une coordination d'équipe plutôt que la solution native gratuite ? |
| Créateurs de harnesses, plateformes agents, intégrateurs | Intégration Core + distribution | Peuvent-ils diffuser Ruu à des utilisateurs auxquels nous n'accédons pas directement ? |
| Grandes organisations multi-fournisseurs | Pilotes ciblés | Y a-t-il un différentiel vérifiable assez important malgré un achat plus lent ? |

La taille de segment et sa priorité ne sont pas prouvées : les entretiens et achats doivent les corriger.

## Positionnement selon l'interlocuteur

- **Développeur** : moins de coordination manuelle Git, de refresh, de merges et d'incertitude entre sessions.
- **Lead** : agents parallèles, mêmes fichiers potentiels, sans prévenir tous les conflits en imposant une exclusivité de modification.
- **CTO** : accroître la capacité de production agentique sans faire augmenter proportionnellement la coordination humaine, si l'économie est démontrée.
- **Éditeur d'outils** : une couche de versioning intégrable et indépendante des fournisseurs qu'il doit autrement construire lui-même.

## Scénarios différenciants à vérifier

Chevauchement réel sur mêmes fichiers ; checkpoints intermédiaires convergés avant fermeture des contributions ; stale work ; retries/crash et effet ambigu ; travail multi-dépôts ; disponibilité exacte d'une dépendance pendant que son producteur continue ; coordination multi-hôtes (Cloud) ; conflit sémantique retourné sans fausse résolution.

Chaque comparatif distingue offre annoncée, livrée, documentée, testée et reproduite. Une offre techniquement partielle peut suffire commercialement à l'acheteur. L'indépendance multi-harness/provider est une **hypothèse de valeur à valider**, pas un monopole technique automatique.
