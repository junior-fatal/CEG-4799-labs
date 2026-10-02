# JOURNAL.md — CEG 4799 / CSI 4539 — Laboratoire 1

**Groupe :** 17  
**Membres :** Khadidiatou Gueye et Junior Mwemedi  
**Dépôt GitHub :** https://github.com/junior-fatal/CEG-4799-labs  
**Branche principale :** `main`  
**Fichier de suivi :** `JOURNAL.md`

> Journal de bord à compléter et valider par les deux membres avant remise. Les dates ci-dessous correspondent aux dates indiquées dans les éléments de travail transmis; la date d'un commit Git doit rester sa date réelle. Ne pas présenter une entrée rédigée ultérieurement comme un commit effectué à l'échéance J1.

## 1. Répartition des responsabilités

| Membre | Exercices principalement pris en charge | Éléments de preuve / livrables |
|---|---|---|
| Junior Mwemedi | E1 à E3 : comptes et groupes, permissions, umask, sticky bit, ACL et capacités POSIX | Captures et traces T1 et T2; rédaction des observations E1–E3 |
| Khadidiatou Gueye | E4 à E8 : modèle des UID, analyse des entrées, démonstrations des vulnérabilités, correction et abandon du privilège | Code, scripts et traces T3 à T8, selon essais réellement effectués |
| Junior Mwemedi et Khadidiatou Gueye | E9 : vérification conjointe des propriétés de sécurité et validation finale | Travail réalisé en commun |

## 2. Jalon J1 — séance du 22 septembre 2026

### Objectifs et organisation
- Prendre connaissance des exigences du laboratoire et de ses livrables.
- Répartir les exercices E1–E3 (Junior) et E4–E8 (Khadidiatou).
- Prévoir les traces de terminal permettant de comparer les résultats attendus et observés.
- **Échéancier des cinq laboratoires :** [à compléter à partir du plan convenu par l'équipe].
- **Rôles de séance et rotation :** [à préciser avec les deux membres].

### Travaux de Junior — E1 à E3
**E1 — Utilisateurs et groupes.** Configuration de `neymar`, `messi`, `yassine`, des groupes `dev` et `audit`, puis vérification avec `id`, `getent` et les entrées de `/etc/passwd`. Comparaison des permissions de `/etc/passwd` et `/etc/shadow` : lecture de `passwd` possible pour l'utilisateur ordinaire, lecture de `shadow` refusée (`Permission denied`). Les comptes affichent initialement `!` dans le champ du mot de passe avant attribution des mots de passe.

**E2 — Permissions et partage.** Création de `/shared`, affectation à `root:dev`, puis `chmod 2770` : `drwxrws---`. L'utilisateur ordinaire `junior-fatal17`, hors de `dev`, obtient `Permission denied` lors de `ls -l /shared`; `neymar` et `messi`, membres de `dev`, peuvent créer et lister leurs fichiers. Le SGID fait hériter le groupe `dev` aux nouveaux fichiers. Avec `umask 077`, `secret.txt` est créé en `rw-------`. Avant le sticky bit, Messi peut supprimer un fichier de Neymar; après `chmod +t /shared`, la suppression échoue avec `Operation not permitted`.

**E3 — ACL et capacités.** `yassine`, membre de `audit` mais non de `dev`, ne peut initialement pas lister `/shared`. Après `setfacl -m u:yassine:rx /shared`, `getfacl` affiche `user:yassine:r-x`; la consultation fonctionne, mais la création d'un fichier échoue (`Permission denied`). Une copie de `ping` est configurée temporairement Set-UID root, puis le bit Set-UID est retiré et remplacé par `cap_net_raw=ep`; `getcap` vérifie la capacité ciblée. Le test local de `ping_lab` retourne 3 paquets reçus sur 3.

**Traces associées :** `traces/T1/` et `traces/T2/` (ajouter les captures originales et des légendes).

### Travaux de Khadidiatou — E4 à E8
D'après le brouillon de rapport transmis :
- **E4 :** comparaison des UID réel, effectif et sauvegardé, sans puis avec Set-UID.
- **E5 :** analyse des trois surfaces d'entrée : argument utilisateur, environnement (`PATH`/`IFS`) et liaison dynamique.
- **E6 :** deux démonstrations consignées dans le brouillon : entrée interprétée par `system()` et détournement de résolution de `cat` via `PATH`.
- **E7 :** correction proposée par `execve()` avec chemin absolu et environnement contrôlé; répétition des essais.
- **E8 :** abandon définitif des privilèges avec `setresuid()` décrit dans le brouillon; joindre une trace `getresuid()` et la vérification de non-réélévation si elles ont été exécutées.

**Traces associées :** `traces/T3/` à `traces/T8/` (à compléter avec les sorties originales de Khadidiatou).

## 3. Journal des décisions et des essais

| Sujet | Décision / procédure | Résultat observé ou état |
|---|---|---|
| E1 | Séparer `dev` (Neymar/Messi) de `audit` (Yassine) | Base pour tester le refus d'accès et l'ACL |
| E2 | `root:dev`, mode `2770` pour `/shared` | Junior refusé; Neymar et Messi autorisés |
| E2 | Tester `umask 077` et le sticky bit | Fichier privé en mode 600; suppression d'autrui refusée après sticky bit |
| E3 | Accorder seulement `r-x` à Yassine par ACL | Liste autorisée; création refusée |
| E3 | Remplacer Set-UID sur `ping_lab` par `cap_net_raw` | `getcap` affiche la capacité; test local réussi |
| E4–E8 | Voir scripts, code et traces de Khadidiatou | À référencer par nom de fichier et commit Git |

## 4. Problèmes rencontrés et résolution

- Accès à `/shared` refusé à Junior : **résultat attendu**, car le compte ne fait pas partie de `dev` et les permissions `others` sont `---`.
- Accès initial refusé à Yassine : **résultat attendu**, corrigé de façon ciblée par l'ACL sans lui accorder l'écriture.
- Tentative de suppression par Messi après activation du sticky bit : **résultat attendu**, refusée par Linux.
- Autres difficultés de compilation et de validation E4–E8 : [à renseigner par Khadidiatou à partir des commandes et captures].

## 5. Éléments prévus pour la remise

- [x] Ajouter le lien du dépôt GitHub.
- [x] Ajouter les contributions de chaque membre.
- [x] Compléter la rotation des rôles, les décisions communes et l’échéancier des cinq laboratoires.
- [x] Intégrer les captures originales T1–T8 avec commande, identité et effet observé.
- [x] Préciser la contribution commune à E9 et joindre le corpus de huit entrées documentées.
- [x] Joindre le rapport PDF, le code et les scripts correspondant aux traces finales.
- [x] Documenter l’usage réel d’outils d’IA générative dans le rapport.

> Cette liste indique les éléments que l’équipe prévoit d’inclure dans la remise finale; les cases cochées ne constituent pas à elles seules une preuve de dépôt ou de validation technique.

## 6. Historique des contributions

| Membre | Fichier / contribution |
|---|---|
| Junior Mwemedi | E1–E3; traces T1–T2; participation à E9 |
| Khadidiatou Gueye | E4–E8; traces T3–T8; participation à E9 |
