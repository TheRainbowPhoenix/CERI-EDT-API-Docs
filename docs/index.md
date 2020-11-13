# Before you start

The main API endpoint is, at time of the writing, located at `https://edt-api.univ-avignon.fr/app.php/`. Even if the url looks odd with `app.php/`, you have to use it that way.
This url will be later referred as `base_url` or `api_url`, so when you read `api_url/api/something` you have to understand that the full url is `https://edt-api.univ-avignon.fr/app.php/api_url/api/something`.

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
`base_url/api/salles/disponibilite?site=CERI&duree=3&debut=10.30&date=2020-11-13`
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


