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
curl "https://sawpe.madkom.pl/api/archive/4725d2b6cb62b8ee8a7efcbf65d08d0fd52967b4949e615a7d825ff1643e1ed2.pdf?lang=pl"
```

```php
<?php
    $client = new \GuzzleHttp\Client();
    $response = $client->request('GET', 'https://sawpe.madkom.pl/api/archive/4725d2b6cb62b8ee8a7efcbf65d08d0fd52967b4949e615a7d825ff1643e1ed2.pdf?lang=pl');
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
    $response = $client->request('GET', 'https://sawpe.madkom.pl/api/file/bedb4a5ccf66886cdbe5ff656176fe248416bd78f0324f6dd79206c9e5cc7eae');
?>
```

```shell
curl "https://sawpe.madkom.pl/api/file/bedb4a5ccf66886cdbe5ff656176fe248416bd78f0324f6dd79206c9e5cc7eae"
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

> Przykładowy JSON zwrotny dla podpisu typu XAdES:

```json
{
    "appID": "sawpe.madkom.pl",
    "dateRequest": "2017-04-14 9:53:26",
    "idRequest": "5f05ac9c3b54d23d8dd607d5f312b7936094a9c091a7f586ad8557f91d06a17a",
    "requestFiles": [
        "print_20170221095349.pdf.xades",
        "print_20170221095349.pdf"
    ],
    "signatureFiles": [
        {
            "fileName": "print_20170221095349.pdf.xades",
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
                                "issuer": "serialNumber=Nr wpisu: 14, C=PL, O=Asseco Data Systems S.A., CN=CERTUM QCA",
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
                                    "notAfter": "2018-04-28",
                                    "notBefore": "2016-04-28"
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
                                "name": "serialNumber=Nr wpisu: 14, C=PL, O=Asseco Data Systems S.A., CN=CERTUM QCA",
                                "selfSigned": false,
                                "serialNumber": "29592591606546924594270077441325992323698688547",
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
                                    "notBefore": "2016-04-01"
                                }
                            },
                            {
                                "download": null,
                                "generalStatus": "CORRECT",
                                "isPzCA": false,
                                "isQualified": false,
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
                    "pdfSignatureResult": null,
                    "signatureResult": {
                        "reference": [
                            {
                                "externalFile": true,
                                "idReference": "Reference1_f2eaf393-669f-4a61-9709-e5440aa06334_72",
                                "status": "CORRECT",
                                "uri": "print_20170221095349.pdf"
                            },
                            {
                                "externalFile": false,
                                "idReference": "SignedProperties-Reference_f2eaf393-669f-4a61-9709-e5440aa06334_7d",
                                "status": "CORRECT",
                                "uri": "#SignedProperties_f2eaf393-669f-4a61-9709-e5440aa06334_1b"
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
                    "signer": "Bartosz Tomaszewski",
                    "signingCertificateStatus": {
                        "status": "CORRECT"
                    },
                    "signingTimeResult": {
                        "time": "2017-02-21 08:54:31",
                        "type": "DECLARED"
                    },
                    "timestampStatus": {
                        "certificateStatus": null,
                        "timestampExist": false,
                        "timestampValue": "2017-04-14 09:53:24",
                        "verificationStatus": "UNCHECKED"
                    },
                    "unsignedProperties": {
                        "certificateRefsResult": [
                            {
                                "id": null,
                                "status": "NOT_EXISTS",
                                "subjectDN": null
                            }
                        ],
                        "revocationRefsResult": [
                            {
                                "id": null,
                                "status": "NOT_EXISTS"
                            }
                        ]
                    }
                },
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
                                "issuer": "serialNumber=Nr wpisu: 14, C=PL, O=Asseco Data Systems S.A., CN=CERTUM QCA",
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
                                    "notAfter": "2018-04-28",
                                    "notBefore": "2016-04-28"
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
                                "name": "serialNumber=Nr wpisu: 14, C=PL, O=Asseco Data Systems S.A., CN=CERTUM QCA",
                                "selfSigned": false,
                                "serialNumber": "29592591606546924594270077441325992323698688547",
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
                                    "notBefore": "2016-04-01"
                                }
                            },
                            {
                                "download": null,
                                "generalStatus": "CORRECT",
                                "isPzCA": false,
                                "isQualified": false,
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
                    "nodePath": "/ds:Signature/ds:Object/xades:QualifyingProperties/xades:UnsignedProperties/xades:UnsignedSignatureProperties/xades:CounterSignature/ds:Signature",
                    "parentNodePath": "/ds:Signature",
                    "pdfSignatureResult": null,
                    "signatureResult": {
                        "reference": [
                            {
                                "externalFile": false,
                                "idReference": "Reference1_da9736e8-37bf-4df3-8d33-8188abe99936_7c",
                                "status": "CORRECT",
                                "uri": "#SignatureValue_f2eaf393-669f-4a61-9709-e5440aa06334_07"
                            },
                            {
                                "externalFile": false,
                                "idReference": "SignedProperties-Reference_da9736e8-37bf-4df3-8d33-8188abe99936_73",
                                "status": "CORRECT",
                                "uri": "#SignedProperties_da9736e8-37bf-4df3-8d33-8188abe99936_15"
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
                    "signer": "Bartosz Tomaszewski",
                    "signingCertificateStatus": {
                        "status": "CORRECT"
                    },
                    "signingTimeResult": {
                        "time": "2017-02-21 08:56:44",
                        "type": "DECLARED"
                    },
                    "timestampStatus": {
                        "certificateStatus": null,
                        "timestampExist": false,
                        "timestampValue": "2017-04-14 09:53:24",
                        "verificationStatus": "UNCHECKED"
                    },
                    "unsignedProperties": {
                        "certificateRefsResult": [
                            {
                                "id": null,
                                "status": "NOT_EXISTS",
                                "subjectDN": null
                            }
                        ],
                        "revocationRefsResult": [
                            {
                                "id": null,
                                "status": "NOT_EXISTS"
                            }
                        ]
                    }
                }
            ]
        }
    ],
    "status": "ok",
    "verificationStatus": "CORRECT"
}
```

> Przykładowy JSON zwrotny dla podpisu typu PAdES:

```json
{
    "appID": "sawpe.madkom.pl",
    "dateRequest": "2017-04-14 9:17:37",
    "idRequest": "5f05ac9c3b54d23d8dd607d5f312b7936094a9c091a7f586ad8557f91d06a17a",
    "requestFiles": [
        "D2017003077001.pdf"
    ],
    "signatureFiles": [
        {
            "fileName": "D2017000078001.pdf",
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
                                "download": {
                                    "originalName": "Mikołaj Baranowski.pem"
                                },
                                "generalStatus": "CORRECT",
                                "isPzCA": false,
                                "isQualified": false,
                                "isTimestampCA": false,
                                "isTrusted": false,
                                "issuer": "serialNumber=Nr wpisu: 14, C=PL, O=Asseco Data Systems S.A., CN=CERTUM QCA",
                                "name": "CN=Mikołaj Baranowski, GN=Mikołaj, SN=Baranowski, serialNumber=PESEL:24072238446, C=PL",
                                "selfSigned": false,
                                "serialNumber": "120502833778484445328069050452749185273",
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
                                    "notAfter": "2018-07-10",
                                    "notBefore": "2016-07-10"
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
                                "name": "serialNumber=Nr wpisu: 14, C=PL, O=Asseco Data Systems S.A., CN=CERTUM QCA",
                                "selfSigned": false,
                                "serialNumber": "29592591606546924594270077441325992323698688547",
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
                                    "notBefore": "2016-04-01"
                                }
                            },
                            {
                                "download": null,
                                "generalStatus": "CORRECT",
                                "isPzCA": false,
                                "isQualified": false,
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
                    "nodePath": "Signature-271279d4-8c43-4d7c-acfc-bd4ce0cd7b9b",
                    "parentNodePath": null,
                    "pdfSignatureResult": {
                        "isLastSignature": true,
                        "isModified": false,
                        "isWholeDocument": true,
                        "revision": "1",
                        "signedDocument": "D2017003077001.pdf"
                    },
                    "signatureResult": null,
                    "signatureStatus": "CORRECT",
                    "signatureType": {
                        "type": "PADES"
                    },
                    "signedFileInfo": {
                        "originalName": "D2017003077001.pdf_1.pdf"
                    },
                    "signer": "Mikołaj Baranowski",
                    "signingCertificateStatus": {
                        "status": "DOESNT_EXIST"
                    },
                    "signingTimeResult": {
                        "time": "2017-04-13 14:00:52",
                        "type": "TIMESTAMP"
                    },
                    "timestampStatus": {
                        "certificateStatus": {
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
                                    "isTimestampCA": true,
                                    "isTrusted": false,
                                    "issuer": "C=PL, O=Minister wlasciwy do spraw gospodarki, CN=Narodowe Centrum Certyfikacji (NCCert)",
                                    "name": "serialNumber=Nr wpisu: 15, C=PL, O=Asseco Data Systems S.A., CN=CERTUM QTSA",
                                    "selfSigned": false,
                                    "serialNumber": "227792606299944004592002222445418427029064848827",
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
                                        "notBefore": "2016-04-01"
                                    }
                                },
                                {
                                    "download": null,
                                    "generalStatus": "CORRECT",
                                    "isPzCA": false,
                                    "isQualified": false,
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
                        "timestampExist": true,
                        "timestampValue": "2017-04-13 14:00:52",
                        "verificationStatus": "CORRECT"
                    },
                    "unsignedProperties": null
                }
            ]
        }
    ],
    "status": "ok",
    "verificationStatus": "CORRECT"
}
```

Ten endpoint pozwala przesłać pliki do weryfikacji.

Istnieje możliwość przesłania opcjonalnych tablic white_list i black_list z nazwami plików.
Lista przesyłanych plików do weryfikatora będzie wtedy okrojona: dodane zostaną tylko pliki z white listy (jeżeli jest ustawiona) i usunięte
pliki znajdujące się na black liście.

### HTTP Request

`POST https://sawpe.madkom.pl/api/verify`


### Parametry URL

Parametr | Opis
--------- | -----------
white_list | tablica z nazwami plików, które powinny być zweryfikowane (parametr opcjonalny)
black_list | tablica z nazwami plików, które nie powinny być weryfikowane (parametr opcjonalny)

