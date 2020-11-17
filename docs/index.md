# Before you start

The main API endpoint is, at time of the writing, located at `https://edt-api.univ-avignon.fr/app.php/`. Even if the url looks odd with `app.php/`, you have to use it that way.
This url will be later referred as `GET` or `POST`, so when you read `GET api/something` you have to understand that the full url is a `GET` request at `https://edt-api.univ-avignon.fr/app.php/api/something`.

## Basic REST answer

The answer of most of (any ?) api call is a JSON object, matching the following structure :

```js
{
  "results": [
    {
        // Something meaningful ...
    }
}
```
So that, checking if `results` is a key of the root JSON could get you a quick idea on how valid the answer is. I've discovered that sometimes the error code is served over the `HTTP 200` answer, so don't forget to check this too !

# Working with *Salles*

## *Salle* object

| field        | type                | description |
| -----------: | :------------------ | :---------- |
| name         | String with spaces  | The name given to the classroom |
| code         | A-Z0-9_ String code | The internal codename of the classroom |
| searchString | String              | Meaningful items for searches |

## *Sites* available

 - `AGROSCIENCES`, sometimes referred as `Agrosciences` (so it'd good to ignore case checks) 
 - `CERI`
 - `Centre-ville`
 - `IUT`


## `GET api/salles`

Get all classroom on the whole campus. The `Salle` object is listed under a `name` list, identified by a `letter` string.

JSON answer example :
```js
{
  "results": [
    {
      "letter": "Agrosciences",
      "names": [
        {
          "name": "a002 (cmi) v",
          "code": "AGR_A002",
          "searchString": "a002(cmi)vAgrosciencesa002 (cmi) v"
        }
      ]
    },
    {
      "letter": "CERI",
      "names": [
        {
          "name": "amphi ada",
          "code": "CERI_ADA",
          "searchString": "amphiadaCERIamphi ada"
        }
      ]
    }
  ]
}
```

## `GET api/salles/disponibilite`

Get available classrooms for the given campus at a given time.

The `GET` request had to include the following parameters :

| field        | example             | description |
| -----------: | :------------------ | :---------- |
| site         | CERI                | One of the previously listed `Sites` |
| duree        | 3                   | Time in hours, with `.30` for -"h30" |
| debut        | 10.30               | Start hour, with `.30` for -"h30 |
| date         | 2020-11-13          | Date in YYYY-MM-DD |

And the matching request :
`GET api/salles/disponibilite?site=CERI&duree=3&debut=10.30&date=2020-11-13`
That would produce a similar output :
```js
{
  "results": [
    {
      "libelle": "cisco = c 133 réseaux",
      "capacite": "26"
    },
    {
      "libelle": "s6 = c 022",
      "capacite": "50"
    }
  ]
}
```

The `debut` can hold the following values :

| value   | Time value |
| :------ | ---------: |
| 8       | 8h00       |
| 8.30    | 8h30       |
| 9       | 9h00       |
| 9.30    | 9h30       |
| 10      | 10h00      |
| 10.30   | 10h30      |
| 11      | 11h00      |
| 11.30   | 11h30      |
| 12      | 12h00      |
| 12.30   | 12h30      |
| 13      | 13h00      |
| 13.30   | 13h30      |
| 14      | 14h00      |
| 14.30   | 14h30      |
| 15      | 15h00      |
| 15.30   | 15h30      |
| 16      | 16h00      |
| 16.30   | 16h30      |
| 17      | 17h00      |
| 17.30   | 17h30      |
| 18      | 18h00      |
| 18.30   | 18h30      |
| 19      | 19h00      |
| 19.30   | 19h30      |

The `duree` can hold the following values :

| value   | Time value |
| :------ | ---------: |
| 1.30    | 1h30       |
| 3       | 3h00       |
| 4.30    | 4h30       |
| 6       | 6h00       |


All the objects under `results` are `SalleDisponibilite`, described as follow :

## *SalleDisponibilite* object

| field        | type                | description |
| -----------: | :------------------ | :---------- |
| libelle      | String with spaces  | The name given to the classroom |
| capacite     | Number in String    | The max count of people that the classroom can have. |

## `GET api/events_salle/{str:code}`

Get classroom event for the given codename. (see: *Salle:code*) For the `amphi ada`, you'll query the following url `GET api/events_salle/CERI_ADA` where `CERI_ADA` is the code of `amphi ada` that you fetched from `GET api/salles`. A `capacity` field will be also returned, telling you how much student the classroom can hold.

JSON answer example :
```js
{
  "results": [
    {
      "code": null,
      "start": "2020-09-14T08:00:00+00:00",
      "end": "2020-09-14T09:30:00+00:00",
      "title": "Matière : Rentree specifique\nEnseignants : BENSLIMANE Abderrahim, DUFOUR Richard, LABATUT Vincent\nPromotions : M1 INGE DU LOGICIEL DE LA SOCIETE NUM (ILSEN), M1 INTELLIGENCE ARTIFICIELLE (IA), M1 SYSTEMES INFORMATIQUES COMMUNICANTS (SICOM), M2 INGE DU LOGICIEL DE LA SOCIETE NUM (ILSEN), M2 INTELLIGENCE ARTIFICIELLE (IA), M2 SYSTEMES INFORMATIQUES COMMUNICANTS (SICOM)\nTD : 2, PRE-RENTREES SPECIFIQUES 3\nSalles : Amphi Ada, Amphi Blaise\nMémo : \"CERI\"\n",
      "type": "",
      "memo": null
    },
    {
      "code": null,
      "start": "2020-09-14T09:30:00+00:00",
      "end": "2020-09-14T10:30:00+00:00",
      "title": "Matière : Reservation de salles\nEnseignant : GUEYE Serigne\nPromotions : L3 INFORMATIQUE, M2 INGE DU LOGICIEL DE LA SOCIETE NUM (ILSEN), M2 INTELLIGENCE ARTIFICIELLE (IA), M2 SYSTEMES INFORMATIQUES COMMUNICANTS (SICOM)\nTD : 1\nSalle : Amphi Ada\nMémo : \"réunion des stages\"\n",
      "type": "",
      "memo": null
    }
  ],
  "capacite": "129"
}
```

Return objects under `results` :

| field        | type                | description |
| -----------: | :------------------ | :---------- |
| code         | String with spaces  | The name given to the classroom |
| start        | Date    | The beginning date of the event |
| end          | Date    | The finishing date of the event |
| title        | String  | `\n` delimited `key : Value` map. Note that is the `key` ends with `s` it'll be an `, ` delimited array |
| type         | String    | Refers to the [following section](#salle-event-types) |
| memo         | String    | A special information about the event. Almost always `null`. |

> *Note :* The `code` seems to always be a null value. I've also almost never seen the value of `memo` set to something.

## *Salle* event types

 - `CM` "Cours Magistral"
 - `Hybride`
 - `Annulation` lesson is still present on the EDT (greyed out)
 - `UEO`
 - `Evaluation`
 - `TP`
 - `TD`
 - `Rattrapage`

> *Note :* This may change. Feel free to open a pull request if so.

# Working with *Elements*

## *Element* object

| field        | type                | description |
| -----------: | :------------------ | :---------- |
| name         | String with spaces  | The name given to the class |
| code         | A-Z0-9_ String code | The internal codename of the class |
| searchString | String              | Meaningful items for searches |


## `GET api/elements`

Get all classes on the whole campus. The `Element` object is listed under a `name` list, identified by a `letter` string.

JSON answer example :
```js
{
  "results": [
    {
      "letter": "INFORMATIQUE",
      "names": [
        {
          "name": "m2 inge du logiciel de la societe num (ilsen)",
          "code": "2-M2EN",
          "searchString": "INFORMATIQUEm2 inge du logiciel de la societe num (ilsen)"
        },
      ]
    }
  ]
}
```

> *Note :* The author of this unofficial documentation would like to raise the point that there exist classes under the `null` category. Be careful and always check everything !

# Working with *Matieres*

## *Matiere* object

| field        | type                | description |
| -----------: | :------------------ | :---------- |
| name         | String with spaces  | The name given to the subject |
| code         | A-Z0-9_ String code | The internal codename of the subject |
| searchString | String              | Meaningful items for searches |

## `GET api/matieres/UE`

Get all the `UE` (*Unité d'Enseignement*) available.

```js
{
  "results": [
    {
      "letter": "u",
      "names": [
        {
          "name": "uce 2 securite des services web",
          "code": "S-E06-0662",
          "searchString": "uce 2 securite des services web"
        }
      ]
    },
    {
      "letter": "(",
      "names": [
        {
          "name": "(option) botanique",
          "code": "S-U02-1046",
          "searchString": "(option) botanique"
        },
        {
          "name": "(option) methodes d'inventaires faunistiques",
          "code": "S-U02-1047",
          "searchString": "(option) methodes d'inventaires faunistiques"
        }
      ]
    }
  ]
}
```

It's a bit special since you have to work with first letter of the name (given that you can't really known it at first) but it could work like 90% if you use the `u` (since most UE begin with "uce" or "ue")

## `GET api/matieres/UEO`

Get all the `UEO` (*Unité d'Enseignement d'Ouverture*) available.

```js
{
  "results": [
    {
      "letter": "u",
      "names": [
        {
          "name": "ueo activite culturelle s1",
          "code": "T-U07-9201",
          "searchString": "ueoactiviteculturelles1"
        },
        {
          "name": "ueo activite culturelle s2",
          "code": "T-U07-9202",
          "searchString": "ueoactiviteculturelles2"
        }
      ]
    }
  ]
}

```

> *Note :*  All the `UEO` starts with "ueo" so that you shouldn't get anything else than the letter `u`.

# Working with *Enseignants*

## *Enseignant* object

| field        | type                | description |
| -----------: | :------------------ | :---------- |
| name         | String with spaces  | The name given to the subject |
| code         | A-Z0-9_ String code | The internal codename of the subject |
| uapvHarpege  | A-Z0-9_ String code | Harpège (RH for universities) identifier |
| searchString | String              | Meaningful items for searches |

## `GET api/enseignants`

Get all the `Enseignant` (*Enseignant*) available.

```js
{
  "results": [
    {
      "letter": "G",
      "names": [
        {
          "name": "Gordon Freeman",
          "code": "1337",
          "uapvHarpege": "420",
          "searchString": "Gordon Freeman"
        }
      ]
    },
    {
      "letter": "T",
      "names": [
        {
          "name": "Tamamo No Mae",
          "code": "8888",
          "uapvHarpege": "fox 001",
          "searchString": "Tamamo No Mae"
        }
      ]
    },
  ]
}
```

## `GET api/events_enseignant/{int:uapvHarpege}`
Get all the `Enseignant` (*Enseignant*) events.

```js
{
  "results": [
    {
      "code": null,
      "start": "2020-11-06T09:00:00+00:00",
      "end": "2020-11-06T10:30:00+00:00",
      "title": "Matière : BIOLOGIE DE LA REPRODUCTION VE\nEnseignant : Gordon Freeman\nTD : Gr A TP, Gr B TP, Gr C & CMI TP, Gr E TP, Gr F TP\nSalle : Amphithéatre AGROSC\nType : CM\n",
      "type": "CM",
      "memo": null
    },
    {
      "code": null,
      "start": "2020-12-18T07:30:00+00:00",
      "end": "2020-12-18T11:30:00+00:00",
      "title": "Annulation : \nMatière : BIOLOGIE DE LA REPRODUCTION VE\nEnseignant : Gordon Freeman\nTD : Gr B TP\nSalle : TP B123 (Biologie microscopie)\nType : TP\n",
      "type": "Annulation",
      "memo": null
    }
  ]
}
```

| field        | type    | description |
| -----------: | :------ | :---------- |
| code         | String? | Maybe some internal code |
| start        | Date    | The beginning date of the event |
| end          | Date    | The finishing date of the event |
| title        | String  | `\n` delimited `key : Value` map. Note that is the `key` ends with `s` it'll be an `, ` delimited array |
| type         | String  | See the ["*Salle* event types"](#salle-event-types) |
| memo         | String  | A special information about the event. Almost always `null`. Ingored in parsing. |

> *Note :* The `code` seems to always be a null value. I've also almost never seen the value of `memo` set to something.


# Using the *ressouce* API

> *Warning* I don't kown how useful this is, so it may be a good idea to IGNORE THIS.

## Get more infos about the *Salle*

> *Info :* The result of this page is HTML, not JSON !

You can get some informations about the *Salle* at the following url:
`https://ressource.univ-avignon.fr/searchSalle?salle={s}`

Where `s` is the string you get under the `Salle : ` field on the `title` key of a *Salle*, like thoses fetched at `GET api/events_salle/{str:code}`.

Like, if you want the `Amphi Ada` informations (not so relevent) : `https://ressource.univ-avignon.fr/searchSalle?salle=Amphi%20Ada` (`%20` mean space character in url encoding)
You'll (probably) get the following informations :


| Vidéoprojecteur | |    |
| :- | :----------- | :- |
|    | Nombre       | 1  |
|    | Connectivité |    |
|    | Marque       | Sanyo XGA |

And if you clic the "En savoir plus" button, you'll get more :

| Vidéoprojecteur | |    |
| :- | :----------- | :- |
|    | Nombre       | 1  |
|    | Connectivité | VGA |
|    | Marque       | Sanyo XGA |
|    | Description  | VP 2600 L |
|    | Date d'achat | 01/01/2011 |
|    | Remarques    | connectivité VGA |

But wait, here's more !

Let's now try with the `cisco` classroom : `https://ressource.univ-avignon.fr/searchSalle?salle=cisco`
You'll get the complete list of custom softwares on it !

## Finding where is a *Logiciel* (software) installed

> *Info :* The result of this page is HTML, not JSON !

You can get some informations about the *Logiciel* at the following url:
`https://ressource.univ-avignon.fr/searchLogiciel?logiciel={s}&id={i}`
Where `s` is the internal name of the *Logiciel* and `i` its internal ID.

It seems that using this URL only with the internal name or ID work fine too.
For now, we'll only use the `logiciel` field.

You want to kown where is `CISCO Packet Tracer` available ?
`https://ressource.univ-avignon.fr/searchLogiciel?logiciel=CISCO+Packet+Tracer` (or `https://ressource.univ-avignon.fr/searchLogiciel?logiciel=CISCO+Packet+Tracer&id=92`)

Under the "Salles" you'll find a list of every classroom where the software is installed.

## Get the complete list

### Logiciels

`GET https://ressource.univ-avignon.fr/json/logiciels`
Get all the `logiciels` (*Logiciels*, software) available.

```js
[
  {
    "Nom": "7-zip 19.00 (Version : 19.00, Catégorie : )",
    "NbLicence": null,
    "Id": 269,
    "Version": "19.00",
    "Licence": false,
    "Demandeur": "DOSI",
    "Os": null,
    "Installation": "05/06/2019",
    "Remarque": "",
    "Salle": {
      "agroparc": "",
      "cv": "<a href=\"/searchSalle?salle=2w14+info\">2w14 info</a><br/> ,<a href=\"/searchSalle?salle=2w28+%28geo%29\">2w28 (geo)</a><br/> ,<a href=\"/searchSalle?salle=2w29+info\">2w29 info</a><br/> ,<a href=\"/searchSalle?salle=2w30+info\">2w30 info</a><br/> ,<a href=\"/searchSalle?salle=2w31+info\">2w31 info</a><br/> ,<a href=\"/searchSalle?salle=2w38+info\">2w38 info</a><br/> ,<a href=\"/searchSalle?salle=2w39+info\">2w39 info</a><br/> ,<a href=\"/searchSalle?salle=004+%2824+%C3%A9tudiants+%2B+1+formateur%29\">004 (24 étudiants + 1 formateur)</a><br/> ,<a href=\"/searchSalle?salle=1e02\">1e02</a><br/> ,<a href=\"/searchSalle?salle=1w25+%28com%29\">1w25 (com)</a><br/> ,<a href=\"/searchSalle?salle=2e16+cartographie+libre+acc%C3%A8s\">2e16 cartographie libre accès</a><br/> ,<a href=\"/searchSalle?salle=2w13+info\">2w13 info</a><br/> "
    },
    "Catégorie": null
  },
  {
    "Nom": "Adobe Dreamweaver (Version : CS 4, Catégorie : Éditeur)",
    "NbLicence": 12,
    "Id": 186,
    "Version": "CS 4",
    "Licence": true,
    "Demandeur": "REDACTED FOR PRIVACY",
    "Os": null,
    "Installation": "01/09/2015",
    "Remarque": "",
    "Salle": {
      "agroparc": "",
      "cv": ""
    },
    "Catégorie": "Éditeur"
  },
]
```

Where `cv` mean "Centre Ville".

You may also search for it :
`GET https://ressource.univ-avignon.fr/json/logiciels?name={name}&doublon={true|false}`

If you want to find "Chrome" for example:
`GET https://ressource.univ-avignon.fr/json/logiciels?name=Chrome&doublon=true`

```js
[
  {
    "Nom": "Chrome (Version : , Catégorie : )",
    "NbLicence": null,
    "Id": 178,
    "Version": "",
    "Licence": false,
    "Demandeur": "REDACTED FOR PRIVACY",
    "Os": null,
    "Installation": "04/01/2016",
    "Remarque": "",
    "Salle": {
      "agroparc": "<a href=\"/searchSalle?salle=cisco+%3D+c+133+r%C3%A9seaux\">cisco = c 133 réseaux</a><br/> ,<a href=\"/searchSalle?salle=res+%3D+c+132+reseaux\">res = c 132 reseaux</a><br/> ,<a href=\"/searchSalle?salle=cisco+%3D+c+133+r%C3%A9seaux\">cisco = c 133 réseaux</a><br/> ,<a href=\"/searchSalle?salle=res+%3D+c+132+reseaux\">res = c 132 reseaux</a><br/> ,<a href=\"/searchSalle?salle=cisco+%3D+c+133+r%C3%A9seaux\">cisco = c 133 réseaux</a><br/> ,<a href=\"/searchSalle?salle=res+%3D+c+132+reseaux\">res = c 132 reseaux</a><br/> ,<a href=\"/searchSalle?salle=cisco+%3D+c+133+r%C3%A9seaux\">cisco = c 133 réseaux</a><br/> ,<a href=\"/searchSalle?salle=res+%3D+c+132+reseaux\">res = c 132 reseaux</a><br/> ,<a href=\"/searchSalle?salle=cisco+%3D+c+133+r%C3%A9seaux\">cisco = c 133 réseaux</a><br/> ,<a href=\"/searchSalle?salle=res+%3D+c+132+reseaux\">res = c 132 reseaux</a><br/> ",
      "cv": ""
    },
    "Catégorie": null
  }
]
```

### Salles

`GET https://ressource.univ-avignon.fr/json/salles`
Get all the software for a given `salles` (*Salle*, classrom) available.

> *Note :* DON'T DO IT. Seriously, it's like 8MB file with >5s timeout query.

```js
{
  "001 (formation continue)": [
    {
      "Nom": " (FORMATION TOUT AU LONG DE LA VIE)",
      "capacite": 50,
      "av": null,
      "images": "",
      "structure": null
    }
  ],
  "004 (24 étudiants + 1 formateur)": [
      {
      "Nom": "FTLVs004Win109020 (FORMATION TOUT AU LONG DE LA VIE)",
      "capacite": 24,
      "av": null,
      "images": {
        "OS": "Windows",
        "commentaires": "",
        "logiciels": {
          "<a href=\"/searchLogiciel?logiciel=7-zip+19.00&id=269\">7-zip 19.00</a>": {
            "id": 269,
            "version": "19.00",
            "nom": "<a href=\"/searchLogiciel?logiciel=7-zip+19.00&id=269\">7-zip 19.00</a>",
            "date": "05/06/2019",
            "demandeur": "DOSI",
            "licence": false
          },
          "<a href=\"/searchLogiciel?logiciel=Zotero+5&id=74\">Zotero 5</a>": {
            "id": 74,
            "version": "5",
            "nom": "<a href=\"/searchLogiciel?logiciel=Zotero+5&id=74\">Zotero 5</a>",
            "date": "20/09/2019",
            "demandeur": "DOSI",
            "licence": false
          }
        }
      },
      "structure": null
    }
  ]
}
```

You may also search for it :
`GET https://ressource.univ-avignon.fr/json/salles?name={name}`

If you want to find "cisco" for example:
`GET https://ressource.univ-avignon.fr/json/salles?name=cisco`

```js
{
  "cisco = c 133 réseaux": [
    {
      "Nom": "GNU/Linux Multimédia (CERI)",
      "capacite": 26,
      "av": {
        "300": {
          "description": "OPTIPLEX 7010",
          "nom": "Ordinateur fixe",
          "marque": "dell",
          "date": "01/09/2013",
          "remarque": "JS8PJZ1 CT8PJZ1 7T8PJZ1 JP8PJZ1 \n6R8PJZ1 JT8PJZ1 FT8PJZ1 GS8PJZ1\n9S8PJZ1 FS8PJZ1 5Q8PJZ1 BT8PJZ1\n3V8PJZ1 1S8PJZ1 1V8PJZ1 1R8PJZ1\nFR8PJZ1 7S8PJZ1 JZ8PJZ1 1T8PJZ1\n",
          "nombre": 20,
          "connectivite": ""
        }
      },
      "images": {
        "OS": "Linux",
        "commentaires": "GNU/Linux Multimédia",
        "logiciels": {
          "<a href=\"/searchLogiciel?logiciel=Ambulant&id=38\">Ambulant</a>": {
            "id": 38,
            "version": "",
            "nom": "<a href=\"/searchLogiciel?logiciel=Ambulant&id=38\">Ambulant</a>",
            "date": "04/01/2016",
            "demandeur": "REDACTED FOR PRIVACY",
            "licence": false
          }
        }
      },
      "structure": null
    },
    {
      "Nom": "GNU/Linux Projets Réseaux (CERI)",
      "capacite": 26,
      "av": {
        "300": {
          "description": "OPTIPLEX 7010",
          "nom": "Ordinateur fixe",
          "marque": "dell",
          "date": "01/09/2013",
          "remarque": "JS8PJZ1 CT8PJZ1 7T8PJZ1 JP8PJZ1 \n6R8PJZ1 JT8PJZ1 FT8PJZ1 GS8PJZ1\n9S8PJZ1 FS8PJZ1 5Q8PJZ1 BT8PJZ1\n3V8PJZ1 1S8PJZ1 1V8PJZ1 1R8PJZ1\nFR8PJZ1 7S8PJZ1 JZ8PJZ1 1T8PJZ1\n",
          "nombre": 20,
          "connectivite": ""
        }
      },
      "images": {
        "OS": "Linux",
        "commentaires": "GNU/Linux Projets Réseaux",
        "logiciels": {
          "<a href=\"/searchLogiciel?logiciel=Chrome&id=178\">Chrome</a>": {
            "id": 178,
            "version": "",
            "nom": "<a href=\"/searchLogiciel?logiciel=Chrome&id=178\">Chrome</a>",
            "date": "04/01/2016",
            "demandeur": "REDACTED FOR PRIVACY",
            "licence": false
          }
        }
      },
      "structure": null
    }
  ]
}
```


# Digging deeper

Danger ! Do not try this except you DO know what you're doing.

## Exporting Agenda (TODO)
- `/api/exportAgenda/diplome/{formation}`
- `/api/exportAgenda/salle/{salle}`
- `/api/exportAgenda/enseignant/{enseignant:uapvHarpege}`
- `/api/exportAgenda/enseignement/{enseignement}` (Linked to "hyperplanning")
- `/api/exportAgenda/tdoption/{tdoption}`


## List of unexplored API endpoints :
- `/api/elements`
- `/api/enseignants`
- `/api/salles`
- `/api/matieres/EUO`
- `/api/matieres/UE`
- `/api/application`
- `/api/tdoptions`
- `/api/isOpen`
- `/api/auth`
- `/api/auth/login` (POST `application/json`)
- `/api/auth/logout` (POST `application/json`)
- `/api/events_perso`
- `/api/events_promotion`
- `/api/events_tdoption`
- `/api/events_perso_tdoption`
- `/api/events_favori`
- `/api/events_salle`
- `/api/events_enseignant`
- `/api/events_matiere`
- `??/ping` (`api/application/ping`, always return 500)
- `/api/import` (POST `application/json`)

## User api

- `api/users`
- `api/application"`

- `api/users?keyword=`
- `api/users/favoris`
- `api/users/addFavoris` (POST `application/json`)
- `api/users/validate` (POST `application/json`)
- `api/users/{id}/activate` (POST `application/json`)
- `api/users/{id}/` (POST `application/json` = update)

`isStudent` = `uapv` in `uid` (LocalStorage)



## List of useful things
- An `Authorization` token header is under `Basic` + `YTIwZTZhY2EtZWU4My00NGJjLTgwMzMtYjQxZjMwNzhjMmI2OmMxOTlmOWM4LTA1NDgtNGJlNzk2NTUtN2VmN2Q3YmY5ZDIw`. It output as console error and look very hardcoded-alike
- `/api/import`
- `/api/auth`


