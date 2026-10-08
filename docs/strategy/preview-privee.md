# Preview confidentielle de Ruu Core OSS

[Retour à l'index](README.md)

## Situation actuelle

Le README de Ruu indique encore un corpus architectural et des qualifications, pas d'implémentation de production. **Ne prétendre ni disposer d'une preview fonctionnelle ni avoir des résultats utilisateurs tant qu'ils ne sont pas réellement vérifiés.**

## Objectif

Permettre à des développeurs sélectionnés de tester le futur Core avant sa publication publique, tout en conservant le contrôle du calendrier et du dispositif commercial. Le fait qu'ils clonent le dépôt sur leur machine n'est pas en soi un problème ; la priorité est d'éviter la divulgation prématurée aux concurrents.

## Accès GitHub recommandé

Créer un dépôt **privé de preview**, idéalement dans une organisation GitHub distincte du travail interne, avec invitations nominatives et rôle **lecture seule** lorsque possible. Ne pas donner d'accès à la stratégie Cloud, aux autres noms de partenaires, aux négociations ou aux calendriers. Examiner et révoquer les accès devenus inutiles, sans prétendre effacer les clones déjà détenus.

Distribuer des builds identifiés, des instructions, des limites connues, une méthode de vérification de provenance/signature si disponible, et une procédure de désinstallation. Certains ingénieurs auront besoin d'inspecter le source ; prévoir cette option sous conditions adaptées.

## Régime de licence

**Preview privée sous conditions d'évaluation** et **release sous une licence réellement open source** ne sont pas interchangeables. Une licence OSS accorde des droits de redistribution ; ne pas compter sur un NDA pour les supprimer. Avant toute diffusion, définir explicitement la licence ou les conditions prépublication, vérifier les dépendances, les droits et les contrats avec un professionnel compétent.

## Premiers essais sans risque client

1. Entretien sur la gestion actuelle du travail concurrent et ses incidents.
2. Présentation honnête des capacités déjà implémentées et prévues.
3. Test sur dépôt jetable et environnement isolé, sans secrets, sans accès organisation GitHub ou dépôt de production.
4. Reproduire plusieurs agents sur même repo/fichiers, stale work, conflits mécaniques et sémantiques, crash/retry et effets inconnus dans le périmètre applicable.
5. Capturer version exacte, étapes, commandes, états Git avant/après, résultats positifs **et échecs**.
6. Vérifier charge de coordination réellement évitée, capacité de réusage et valeur perçue.
7. Tester le scénario « équivalent natif gratuit » en séparant déclaratif et choix réel.
8. Demander éventuellement introductions et autorisations de référence, **séparément**.

Une démo vidéo de 90 secondes peut aider à recruter, mais doit montrer un comportement reproductible et ne pas se substituer aux tests. Ne pas vendre « aucune intervention humaine » ni « résolution de tous les conflits ».

## Arrêt et sortie de confidentialité

Un pilote plus intégré nécessite permissions et risques explicitement acceptés. Si des conditions critiques de sûreté échouent, suspendre toute installation exposant des dépôts réels.

Si Core est réellement prêt avant Cloud, comparer le bénéfice du secret au coût de la croissance OSS empêchée. La publication OSS anticipée reste une possibilité ; ne pas attendre indéfiniment le produit Cloud. En cas de fuite, appliquer le [plan de réponse](lancement-et-riposte.md) et conserver les faits.
