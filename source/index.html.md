---
title: API Reference

language_tabs:
  - shell
  - php: PHP

toc_footers:
  - <a href='/pl/kontakt' target="_blank">Skontaktuj się z nami</a>
  - <a href='http://madkom.pl' target="_blank">MADKOM SA</a>

search: false
---

# Wstęp

SAWPE umożliwia weryfikowanie plików poprzez API oraz pobieranie wyników archiwalnych w postaci HTML, PDF i JSON.

Dodatkowo istnieje możliwość pobrania wyodrębnionego z podpisu pliku.

# Autoryzacja

Autoryzacja odbywa się poprzez przekazanie klucza autoryzacyjnego w nagłówku api-auth.

Domyślny klucz api-auth to: <code id="api-docs-key">{% jeżeli widzisz ten napis, odśwież stronę %}</code>


```php
<?php
    $headers = array('api-auth' => YOUR_API_KEY);
    $options = array('headers' => $headers);

    $client = new \GuzzleHttp\Client();
    $response = $client->request('METHOD', 'https://sawpe.madkom.pl/api/<PATH>', $options);
?>
```

```shell
curl "https://sawpe.madkom.pl/api/<PATH>"
  -H "api-auth: YOUR_API_KEY"
```


> Pamiętaj aby podać poprawny klucz!

Aby uzyskać własny klucz premium - bez ograniczeń wyszukiwania, skontaktuj się z nami za pomocą <a href='/pl/kontakt' target="_blank">formularza</a>

# Pobieranie archiwalnych

## GET

```shell
curl "https://sawpe.madkom.pl/api/archive/5846c534a31b58315f24a0a4.pdf?lang=pl"
```

```php
<?php
    $client = new \GuzzleHttp\Client();
    $response = $client->request('GET', 'https://sawpe.madkom.pl/api/archive/5846c534a31b58315f24a0a4.pdf?lang=pl');
?>
```



> W przypadku użycia domyślnego klucza autoryzacyjnego, powyższy kod zwróci przekierowanie do formularza z zabezpieczeniem reCAPTCHa (jeżeli obsługiwana jest odpowiedź typu HTML).

> W przypadku płatnego klucza, zwróci załącznik w formacie PDF.

> Podany parametr lang jest opcjonalny, w przypadku nieprzekazania parametru wynik weryfikacji będzie w języku polskim.

Ten endpoint pozwala pobrać archiwalne wyniki przy pomocy identyfikatora weryfikacji.

Identyfikator zawiera w sobie informację, czy skorzystano z domyślnego klucza autoryzacyjnego, czy płatnego.

Identyfikator weryfikacji można znaleźć w jsonie odpowiedzi serwera po weryfikacji, wydruku PDF lub widoku HTML.

Istnieje możliwość pobrania wyniku weryfikacji w języku angielskim, należy w tym celu dodać parametr lang do zaptytania, tak jak w przykładzie.

### HTTP Request

`GET https://sawpe.madkom.pl/api/archive/<ID>.<TYPE>?lang=<LANG>`

### Parametry URL

Parametr | Opis
--------- | -----------
ID | Identyfikator weryfikacji
TYPE | Typ pobieranego pliku (HTML, PDF lub JSON)
LANG | Język wyniku weryfikacji (pl lub en), opcjonalny


# Pobieranie dokumentu

## GET

SAWPE umożliwia pobranie wyodrębnionego z podpisu pliku.

Informacja o identyfikatorze potrzebnym do pobrania pliku znajduje się w json'ie - wyniku weryfikacji w sekcji <code>signedFileInfo</code>, w kluczu <code>path</code>.

Podpisany plik można pobierać do 24 godzin od momentu weryfikacji podpisu. Po tym czasie plik jest usuwany z bazy.

```php
<?php
    $client = new \GuzzleHttp\Client();
    $response = $client->request('GET', 'https://sawpe.madkom.pl/api/file/58931fdebc2cc100db6c7d25');
?>
```

```shell
curl "https://sawpe.madkom.pl/api/file/58931fdebc2cc100db6c7d25"
```

> Uwaga!
Sekcja signedFileInfo jest prezentowana tylko w wyniku weryfikacji podpisu elektronicznego. W przypadku pobierania archiwalnych wyników klucz ten jest ustawiany na null


### HTTP Request

`GET https://sawpe.madkom.pl/api/file/<FILE_ID>`

### Parametry URL

Parametr | Opis
--------- | -----------
FILE_ID | Identyfikator pliku pobrany z klucza path z signedFileInfo


# Weryfikacja

## POST

```php
<?php
    $body = fopen('/path/to/file', 'r');
    $headers = array('api-auth' => YOUR_API_KEY);
    $options = array('headers' => $headers, 'body' => $body);

    $client = new \GuzzleHttp\Client();
    $response = $client->request('POST', 'https://sawpe.madkom.pl/api/verify', $options);
?>
```

```shell
curl "https://sawpe.madkom.pl/api/verify"
  -H "api-auth: YOUR_API_KEY"
```

> Powyższy kod zwróci podanego JSON'a:

```json
[
  {
      "appID": "sawpe.madkom.pl",
      "dateRequest": "2016-12-06 14:03:32",
      "idRequest": "5846c534a31b58315f24a8a4",
      "requestFiles": [
          "originalName"
      ],
      "signatureFiles": [
          {
              "fileName": "originalName",
              "fileStatus": "CORRECT",
              "signatureNodesResult": [
                  {
                      "PZVerifyStatus": {
                          "danePZ": null,
                          "status": "NOT_EXISTS"
                      },
                      "certificateResult": {
                          "certificatePathStatus": "CORRECT",
                          "isTrustedChain": "CORRECT",
                          "otherErrors": [],
                          "problemWithPz": false,
                          "reports": [
                              {
                                  "download": null,
                                  "generalStatus": "CORRECT",
                                  "isPzCA": false,
                                  "isQualified": false,
                                  "isTimestampCA": false,
                                  "isTrusted": false,
                                  "issuer": "serialNumber=Nr wpisu: 1, C=PL, O=Unizeto Technologies S.A., CN=CERTUM QCA",
                                  "name": "CN=Bartosz Tomaszewski, GN=Bartosz, SN=Tomaszewski, serialNumber=PESEL:13292762743, C=PL",
                                  "selfSigned": false,
                                  "serialNumber": "50234529",
                                  "statusCrl": {
                                      "status": "CORRECT"
                                  },
                                  "statusOcsp": {
                                      "status": "UNCHECKED"
                                  },
                                  "statusValidity": {
                                      "status": "CORRECT"
                                  },
                                  "validity": {
                                      "notAfter": "2017-03-29",
                                      "notBefore": "2015-03-30"
                                  }
                              },
                              {
                                  "download": null,
                                  "generalStatus": "CORRECT",
                                  "isPzCA": false,
                                  "isQualified": true,
                                  "isTimestampCA": false,
                                  "isTrusted": true,
                                  "issuer": "C=PL, O=Minister wlasciwy do spraw gospodarki, CN=Narodowe Centrum Certyfikacji (NCCert)",
                                  "name": "serialNumber=Nr wpisu: 1, C=PL, O=Unizeto Technologies S.A., CN=CERTUM QCA",
                                  "selfSigned": false,
                                  "serialNumber": "240593320095729088310555193235895822405302832950",
                                  "statusCrl": {
                                      "status": "CORRECT"
                                  },
                                  "statusOcsp": {
                                      "status": "UNCHECKED"
                                  },
                                  "statusValidity": {
                                      "status": "CORRECT"
                                  },
                                  "validity": {
                                      "notAfter": "2019-04-07",
                                      "notBefore": "2014-04-07"
                                  }
                              },
                              {
                                  "download": null,
                                  "generalStatus": "CORRECT",
                                  "isPzCA": false,
                                  "isQualified": true,
                                  "isTimestampCA": false,
                                  "isTrusted": true,
                                  "issuer": "C=PL, O=Minister wlasciwy do spraw gospodarki, CN=Narodowe Centrum Certyfikacji (NCCert)",
                                  "name": "C=PL, O=Minister wlasciwy do spraw gospodarki, CN=Narodowe Centrum Certyfikacji (NCCert)",
                                  "selfSigned": true,
                                  "serialNumber": "563206454069127598643323046419945895619292235545",
                                  "statusCrl": {
                                      "status": "CORRECT"
                                  },
                                  "statusOcsp": {
                                      "status": "UNCHECKED"
                                  },
                                  "statusValidity": {
                                      "status": "CORRECT"
                                  },
                                  "validity": {
                                      "notAfter": "2020-10-26",
                                      "notBefore": "2009-10-26"
                                  }
                              }
                          ],
                          "unvalidChain": false
                      },
                      "commitmentType": [],
                      "nodePath": "/ds:Signature",
                      "parentNodePath": null,
                      "signatureResult": {
                          "reference": [
                              {
                                  "externalFile": false,
                                  "idReference": "Reference1_bbabc72d-1df3-4b5b-b868-02c84d840129_7e",
                                  "status": "CORRECT",
                                  "uri": "#Object1_bbabc72d-1df3-4b5b-b868-02c84d840129"
                              },
                              {
                                  "externalFile": false,
                                  "idReference": "SignedProperties-Reference_bbabc72d-1df3-4b5b-b868-02c84d840129_71",
                                  "status": "CORRECT",
                                  "uri": "#SignedProperties_bbabc72d-1df3-4b5b-b868-02c84d840129_17"
                              }
                          ],
                          "signatureValueStatus": {
                              "status": "CORRECT"
                          }
                      },
                      "signatureStatus": "CORRECT",
                      "signatureType": {
                          "type": "QUALIFIED"
                      },
                      "signedFileInfo": null,
                      "signer": "unknown",
                      "signingCertificateStatus": {
                          "status": "CORRECT"
                      },
                      "signingTimeResult": {
                          "time": "2015-10-20 11:57:17",
                          "type": "DECLARED"
                      },
                      "timestampStatus": {
                          "certificateStatus": null,
                          "timestampExist": true,
                          "timestampValue": "2016-12-06 14:03:31",
                          "verificationStatus": "INCORRECT"
                      }
                  }
              ]
          }
      ],
      "status": "ok",
      "verificationStatus": "CORRECT"
  }
]
```

Ten endpoint pozwala przesłać pliki do weryfikacji.

### HTTP Request

`POST https://sawpe.madkom.pl/api/verify`
