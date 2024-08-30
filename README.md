# Documentation Utilisation

## Configuration des fichiers et des chemins

1. Emplacement des fichiers de configuration :
    - Les fichiers `ordre_comptable.xml` et `configuration.xml` doivent être placés dans le même dossier.
2. Utilisation des flags pour les chemins de fichiers :
    - Lors de l'utilisation des flags tels que `-config-path`, `-log-path` ou `-ecritures-path`, les chemins de fichiers doivent être encadrés par des guillemets doubles (").
    - Exemple : `-config-path "chemin\de\conf"`
3. Spécificités pour chaque flag :
    - `-config-path` :
        - Ce flag doit uniquement contenir le chemin du dossier contenant les fichiers de configuration.
        - Exemple : `-config-path "chemin\de\conf\"`
    - `-log-path` :
        - Ce chemin doit inclure le nom complet du fichier de log.
        - Exemple : `-log-path "chemin\du\fichier.txt"`
    - `-ecritures-path` :
        - Ce chemin doit inclure le nom complet du fichier d’écriture.
        - Exemple : `-ecritures-path "chemin\du\fichier.txt"`
     
Exemple d'execution en ligne de commande : `.\Zeendoc2Compta.exe --config-path "C:\Zeendoc\conf" --log-path "C:\Zeendoc\log\log.txt" --ecritures-path "C:\Zeendoc\ecritures"`

---

# Ordre_Comptable.xml

### Paramètre fixes

`#indexOrd` : index d’ordonnancement des lignes par facture. Reviens à 1 lors d’une nouvelle facture

`#compLibelle` concatène le complément libelle au libellé de la ventilation

`#indexOrd` Index d’ordonnancement 

## Balises et structure des données

1. Balises optionnelles :
    - `<Header>`
    - `<ColName>`
    - `<Footer>`
2. Balise `<Row>` :
    - Représente une ligne d'écriture comptable.
    - Comportement selon le nombre de lignes :
        - Une seule ligne : les avoirs sont représentés par des valeurs négatives.
        - Deux lignes ou plus : les avoirs changent de colonne. La colonne de débit est alors représentée par `mnt_avoir`.
3. Concaténation des données :
    - Format : élément1|séparateur|élément2|séparateur|élément3...
    - Exemple : "toto"|_|custom_n4|SPLENDIDE|custom_d4
4. Séparateurs :
    - Définis entre deux pipes `|`.
    - Peuvent être composés de plusieurs caractères.
    - Exemples :
        - `|-|`
        - `|-:-|`
        - `|sep|`
    - Si aucun séparateur n'est spécifié (ex: `||`), aucune séparation ne sera appliquée.
5. Types d'éléments :
    - Valeurs brutes : encadrées par des guillemets doubles `""`.
        - Ces valeurs sont statiques et peuvent être directement renseignées dans l'ordre comptable.
    - Valeurs dynamiques : non encadrées de guillemets.
        - Exemple : `custom_t1`, `custom_n4`, etc.
    - Les deux types peuvent être utilisés dans la même chaîne de concaténation.

# Paramètres analytiques

Cette section détaille la structure et les paramètres dynamiques utilisés pour la gestion des données analytiques dans le système comptable.

## Structure

- Une seule balise `<Analytical>` est nécessaire pour l'ensemble des données analytiques.
- Chaque ligne analytique suivra le même format défini dans cette balise.
- Les lignes analytiques seront automatiquement positionnées après les lignes HT ou TVA correspondante.

## Paramètres dynamiques

1. `#axeName`
    - Description : Nom de l'axe analytique
    - Référence : Correspond au plan analytique défini dans le fichier de configuration comptable
2. `#montant`  Montant de la ventilation analytique
3. `#ref`  Code de la section analytique

---

# Configuration.xml

Pour tous les champs acceptant uniquement les valeurs 0 ou 1 :

- 0 signifie NON
- 1 signifie OUI

### Authentification

`<ClientSettings>` Contient les informations essentielles pour accéder à l'armoire Zeendoc.

`<urlClient>` 

- Code client unique fourni par Zeendoc
- Format : Chaîne de caractères

`<login>` et `<password>` Identifiant de connexion à votre espace Zeendoc

`<collId>` Identifiant de l'armoire Zeendoc ciblée

`<exportedField>`

- Nom du champ personnalisé pour vérifier l'état d'export du document
- Utilisation :
    - Si renseigné : Le système vérifie ce champ pour déterminer si le document a été exporté
    - Si vide : Le document est considéré comme non exporté

Note: Assurez vous de l'exactitude de ces données pour éviter les problèmes de connexion.

### Parameters

`<DocTypeCustom>` Custom pour le type de doucement “Avoir” ou non

`<IsNeedToUpdateExportField>` Est ce que l’export doit mettre à jour les champs d’export.

`<FieldsToUpdateWithSeparator>` Liste des champs (custom) qui doivent être mis à jour lors de l’export

`<Item>` Doit possédé deux valeurs séparé par un point virgule `;` la premiere valeur c’est le nom du custom et la deuxième c’est son contenue. Si `#datetime` est renseigné il sera remplacé par l’horodatage

`<PlanAnalytic>`

`<AXE_1>` la valeur replacera `#axeName` dans l’ordre comptable. Comme Zeendoc écrit les axe comptable ainsi: AXE_N. Le nom des balise doit suivre cette ordre :

```xml
<PlanAnalytic>
	<AXE_1>nom qui remplace #axname pour l'axe 1</AXE_1>
	<AXE_2>nom qui remplace #axname pour l'axe 2</AXE_2>
</PlanAnalytic>
```

`<Avoir>` Dans cette balise il faudra renseigné les 3 custom correspondant aux customs du TTC, HT et TVA 

`<dateFormat>` Le format de date voulu.

`<DateCustom>` Liste des custom de date qui doivent être formaté par le `<dateFormat>`

`<SearchQuery>` Le nom de la recherche enregistré voulu

`<NeedToBeSplit>` Possède 3 valeur:

1. `<separator>` Renseigner le séparateur qui permet de savoir ou dois être séparer la valeur
2. `<IndexToTake>` Si plusieurs séparateur sont présent dans la valeur, cette balise permet de savoir le quel dois être pris en compte
3. `<name>` Le nom du custom qui possède la valeur qui doit être splitté

Chaque bloc d’info est encapsuler dans une balise `<Item>` . Cette derniere peut etre duplicé autant de fois que necessaire

`<exportSettings>` Possède les deux custom des imputation HT et TVA. (`<ImputationHtCustom>` , `<ImputationTvaCustom>`)

`<WritingSeparator>` Caractère de séparation de l’écriture final. (Uniquement un caractère ou un \t pour la tabulation)

---
