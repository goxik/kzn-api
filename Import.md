# Import dat do Kudy z nudy

Tento dokument popisuje, jak jednoduše sdílet (importovat) vaše články na portál Kudy z nudy.

Portál Kudy z nudy podporuje import pro tyto typy obsahu: akce a aktivity. Hlavním rozdílem typů je doba trvání. Akce mají omezenou dobu trvání (v určitý okamžik začínají a v určitý končí). Aktivity jsou časově neomezené – jedná se o tzv. Turistické cíle. Z pohledu dat jsou si typy velmi podobné. Rozdílem je pouze existence doby trvání u akcí a jiná kategorizace u typu akce a jiná u typu aktivity. Je tedy potřeba rozdělit si obsah na akce a aktivity. Někteří poskytovatelé poskytují pouze akce, někteří pouze aktivity, někteří obojí současně. 

Provozovatelem portálu [Kudy z nudy](https://www.kudyznudy.cz) a poskytovatelem API je agentura [CzechTourism](https://www.czechtourism.cz/). Funkce importu je přístupná pouze pro smluvní partnery CzechTourism. Pro přístup je nutné mít účet na portálu Kudy z nudy a k tomuto účtu mít přidělena patřičná oprávnění. Tato oprávnění zajistí provozovatel portálu. Více informací naleznete na stránce: Kudy z nudy API](https://www.kudyznudy.cz/faq-casto-kladene-otazky/api). 

Provozovatel portálu Kudy z nudy bude potřebovat znát k registraci vašeho datového zdroje: URL adresu zdroje, na které se budou vaše data nacházet. Například: https://www.domena.cz/feed. Je tedy potřeba implementovat (poskytnout) službu (API), která bude následně registrována na portále Kudy z nudy a pomocí níž se budou data od vás stahovat. Služba musí vracet data v podporovaném formátu. Jedná se o jednoduché pole JSON objektů (záznamů). Modely objektů byly navrženy podle vzoru [Otevřených dat](https://opendata.gov.cz), respektive podle [Otevřených formálních norem](https://data.gov.cz/ofn/).

## Ukázka výstupu služby poskytující data pro portál Kudy z nudy

    [
        {
            "@context": "https://www.kudyznudy.cz/kzn/context/v1/dokument.jsonld",
            "typ": "Turistický cíl",
            "id": "630586e5-9541-4099-84ef-f8a35dc8b745",
            "iri": "https://www.kudyznudy.cz/aktivity/komentovane-prohlidky-arealu-zirec-a-domova-sv-jos",
            "název": {
                "cs": "Bylinkové zahrady Žíreč – komentované prohlídky v areálu Žireč a Domova sv. Josefa"
            },
            "vytvořeno": {
                "typ": "Časový okamžik",
                "datum_a_čas": "2022-10-10T10:21:06.7089805"
            },
            "aktualizováno": {
                "typ": "Časový okamžik",
                "datum_a_čas": "2022-10-10T11:44:32.4484005"
            }
        },
        {
            "@context": "https://www.kudyznudy.cz/kzn/context/v1/dokument.jsonld",
            "typ": "Událost",
            "id": "bb96c39e-3b0f-4035-877b-befe1b684536",
            "iri": "https://www.kudyznudy.cz/akce/dynovy-podzim-v-trojske-botanicke-zahrade",
            "název": {
                "cs": "Dýňový podzim v trojské botanické zahradě"
            },
            "vytvořeno": {
                "typ": "Časový okamžik",
                "datum_a_čas": "2016-08-24T17:25:36"
            },
            "aktualizováno": {
                "typ": "Časový okamžik",
                "datum_a_čas": "2022-10-10T12:34:16.884373"
            }
        }
    ]


| **Hodnota** | **Popis hodnoty** |
| --- | --- |
| **@context** | Popis formátu dat – šablona jsonld (JSON schema: [https://www.kudyznudy.cz/kzn/context/v1/dokument.jsonld](https://www.kudyznudy.cz/kzn/context/v1/dokument.jsonld)). Tato šablona slouží k validaci správnosti vašeho datového zdroje. |
| **typ** | *Turistický cíl* nebo *Událost* **(povinné)** |
| id | Unikátní GUID záznamu *(volitelné)* |
| **iri** | Unikání adresa záznamu. Slouží jako unikátní indentifikátor záznamu. Jedná se o URL adresu detailu záznamu pro získání kompletních dat záznamu. **(povinné)** |
| **název** | Název článku / záznamu **(povinné)** |
| **vyvořeno** | Datum a čas vytvoření **(povinné)** |
| **aktualizováno** | Datum a čas poslední změny. Slouží jako informace o změně pro účely aktualizace. **(povinné)** |


Kudy z nudy pravidelně poptává váš datový zdroj a získává tak informace o změnách, které zpracovává. Pro lepší nastavení je dobré znát, jak často dochází k publikaci změn datového zdroje – může se pak na straně Kudy z nudy nastavit, jak často se mají nová data poptávat. 

Doporučujeme také implementovat filtrování datumem (parametr date). Bude pak možné poptat pouze záznamy změněné od určitého datumu a času (od data poslední aktualizace). Nebudou se tedy muset stahovat již zpracované záznamy. https://www.domena.cz/feed?date=2022-10-02 (vrátí pouze záznamy změněné nebo vytvořené od 2.10.2022).

Druhou službu, kterou je potřeba implementovat, je služba pro detail záznamu. Adresa této služby je předávána v hodnotě iri u každého záznamu a je zároveň unikátním identifikátorem záznamu. Služba detailu záznamu musí vracet data opět v podporovaném formátu. Pokud se jedná o detail aktivity (turistického cíle), pak musí vracet JSON objekt validní podle šablony turistického cíle: https://www.kudyznudy.cz/kzn/context/v1/turisticky-cil.jsonld, pokud se jedná o akci (událost) tak musí vracet data validní podle šablony události: https://www.kudyznudy.cz/kzn/context/v1/udalost.jsonld.

Následují ukázky JSON objektů popisují příklady detailu akce a aktivity. Samotné články existující na portále Kudy z nudy lze v tomto formátu samozřejmě také exportovat – více informací naleznete na stránce o Kudy z nudy API: https://github.com/CzechTourism/kzn-api.


### Ukázka výstupu detailu aktivity (turistického cíle)

    {
        "@context": "https://www.kudyznudy.cz/kzn/context/v1/turisticky-cil.jsonld",
        "typ": "Turistický cíl",
        "typ_turistického_cíle": [
            "https://www.kudyznudy.cz/co-chcete-delat/pamatky",
            "https://www.kudyznudy.cz/co-chcete-delat/zazitky",
            "https://www.kudyznudy.cz/co-chcete-delat/zivotni-styl"
        ],
        "kategorie_turistického_cíle": [
            "https://www.kudyznudy.cz/co-chcete-delat/pamatky/cirkevni-pamatky",
            "https://www.kudyznudy.cz/co-chcete-delat/zazitky/organizovane-vylety-a-exkurze",
            "https://www.kudyznudy.cz/co-chcete-delat/zazitky/netradicni-prohlidky"
        ],
        "id": "630586e5-9541-4099-84ef-f8a35dc8b745",
        "iri": "https://www.kudyznudy.cz/aktivity/komentovane-prohlidky-arealu-zirec-a-domova-sv-jos",
        "název": {
            "cs": "Bylinkové zahrady Žíreč – komentované prohlídky v areálu Žireč a Domova sv. Josefa"
        },
        "vytvořeno": {
            "typ": "Časový okamžik",
            "datum_a_čas": "2022-10-10T10:21:06.7089805"
        },
        "aktualizováno": {
            "typ": "Časový okamžik",
            "datum_a_čas": "2022-10-10T11:44:32.4484005"
        },
        "popis": {
            "cs": "Zažijte příběh Areálu Žireč. Navštívíte jedinečné cyklomuzeum, kavárnu i bylinkovou zahradu v místech bývalé jezuitské rezidence. Zaposloucháte se do zvuku unikátní zvonkohry a projdete se po místech, kde působili jezuité. Poznáte dávnou historii i nový příběh pomoci lidem s roztroušenou sklerózou."
        },
        "dlouhý_popis": {
            "cs": "<a href=\"~/aktivity/barokni-areal-zirec\">Areál Žireč</a> se nachází v části obce Žireč ve Dvoře Králové nad Labem. Můžete navštívit krásnou bylinkovou zahradu a posedět v ní s šálkem kávy z kavárny Damián nebo se projít v rozlehlém přírodním parku. Areál je zároveň domovem pro lidi nemocné roztroušenou sklerózou mozkomíšní, kteří žijí v Domově sv. Josefa. Hlavní budova Domova je bývalou barokní jezuitskou rezidencí. Svojí návštěvou podpoříte péči o nemocné i lidi se sníženou pracovní schopností, kteří vás mohou obsloužit v jednotlivých provozech. Návštěvy a komentované prohlídky jsou možné domluvit po celý rok.<br />\r\n<br />\r\nV Areálu naleznete největší cykloexpozici ve střední Evropě. V kostele sv. Anny se nalézá unikátní barokní zvonový klavír na světě &ndash; jediný svého druhu, dále vzácné varhany, jeden z mála dochovaných deskových jezuitských betlémů a mnoho dalších zajímavých předmětů."
        },
        "vhodné_pro_děti": false,
        "vhodné_pro_zvířata": false,
        "za_každého_počasí": false,
        "bezbariériový_přístup": true,
        "časová_náročnost": 1.5,
        "registrace": null,
        "pořadatel": {
            "typ": "Osoba",
            "název": {
                "cs": "Areál Žireč, Domov sv. Josefa"
            },
            "geometrie": {
                "typ": "Lokalita",
                "souřadnice": [
                    50.413444519042969,
                    15.853754043579102
                ]
            },
            "adresa": {
                "typ": "Kontaktní adresa",
                "ulice": "Žireč 1",
                "obec": "Dvůr Králové nad Labem",
                "psč": "54404",
                "kontakt": {
                    "typ": "Kontakt",
                    "email": "mailto:kollova@dsj-zirec.cz",
                    "mobil": "tel:+420491610533",
                    "facebook": "https://www.facebook.com/arealzirec",
                    "instagram": null,
                    "twitter": null,
                    "url": "https://www.arealzirec.cz"
                }
            },
            "kontaktní_osoba": {
                "typ": "Kontaktní osoba",
                "jméno": "Andrea Kollová",
                "email": "mailto:kollova@dsj-zirec.cz",
                "mobil": "tel:+420730144334"
            }
        },
        "umístění": [],
        "kategorie_vstupného": {
            "typ": "Vstupné",
            "cena": {
                "typ": "Částka",
                "výše": 130.0,
                "měna": "https://publications.europa.eu/resource/authority/currency/CZK"
            },
            "snížená_cena": {
                "typ": "Částka",
                "výše": 90.0,
                "měna": "https://publications.europa.eu/resource/authority/currency/CZK"
            },
            "rodinné_vstupné": null,
            "poznámka": {
                "cs": "Nebo pouze kostel 50/30 či cykloexpozice 90/60 Info a rezervace na 731 604 204"
            }
        },
        "otevírací_doba": null,
        "příloha": [
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/a9/a9afa917-20d3-44f7-83be-4abf7f5ceccb.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Bylinková zahrada"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/17/17609ec2-a7bf-46c7-bfe0-5dea754c9365.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Zvonový klavír"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/52/5219812f-c614-4114-a321-2b6b30d288f2.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Bylinková zahrada"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/0b/0b5e7a00-6158-4e2b-9e35-041cb719d7cd.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Kavárna Damián"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/4b/4b9637d3-70d6-4fc7-9e91-2d5dd68f7cd6.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Bylinková zahrada"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/a0/a0791a5c-84f0-416e-b460-cb746baf3871.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Expozice cyklistiky"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/1c/1c2cd7d9-36d8-4a7b-9705-f7b4b8b224e7.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Kostel sv. Anny"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/5f/5ff23727-9b12-46d6-858e-ddd92fc20795.webp?v=20221010111834",
                "autor_díla": null,
                "typ_média": "https://www.iana.org/assignments/media-types/image/webp",
                "název": {
                    "cs": "Areál Žireč"
                },
                "popis": null
            }
        ]
    }

| **Hodnota** | **Popis hodnoty** |
| --- | --- |
| **@context** |Popis formátu dat – šablona jsonld (JSON schema: https://www.kudyznudy.cz/kzn/context/v1/turisticky-cil.jsonld) |
| **typ** | Turistický cíl **(povinné)** |
| **typ_turistického_cíle** | Pole typů turistického cíle  (první je hlavní) **(povinné)** |
| **kategorie_turistického_cíle** | Pole kategorií turistického cíle  (první je hlavní) **(povinné)** |
| id | Unikátní GUID záznamu (volitelné) |
| **iri** | Unikání adresa záznamu **(povinné)** |
| **název** | Název článku **(povinné)** |
| **vyvořeno** | Datum a čas vytvoření **(povinné)** |
| **aktualizováno** | Datum a čas poslední změny **(povinné)** |
| **popis** | Krátká anotace (perex) – prostý text **(povinné)** |
| dlouhý_popis | HTML text pokračující za krátkým popisem (volitelné) |
| **vhodné_pro_děti** | Vhodné pro děti (true / false) **(povinné)** |
| **vhodné_pro_zvířata** | Vhodné pro zvířata (true / false) **(povinné)** |
| **za_každého_počasí** | Za každého počasí (true / false) **(povinné)** |
| **bezbariériový_přístup** | Bezbariérový přístup (true / false) **(povinné)** |
| časová_náročnost | Počet hodin potřebných k návštěvě místa (volitelné) |
| registrace | URL adresa registrace na externím webu (volitelné) |
| **pořadatel** | Adresa, a kontakt na pořadatele **(povinné)** |
| umístění | Místa konání (adresy a kontakty) (volitelné) |
| kategorie_vstupného | Ceny jednotlivých typů vstupného (volitelné) |
| otevírací_doba | Definice otevírací doby v jednotlivých dnech (volitelné) |
| **příloha** | Pole obrázků a jejich metadata **(povinné)** |

### Ukázka výstupu detailu akce (události)

    {
        "@context": "https://www.kudyznudy.cz/kzn/context/v1/udalost.jsonld",
        "typ": "Událost",

    "doba_trvání": [
            {
                "typ": "Časová specifikace",

    "časový_interval": {
                    "typ": "Časový interval",
                    "začátek": {
                        "typ": "Časový okamžik",

    "datum_a_čas": "2022-10-29T10:00:00"
                    },
                    "konec": {
                        "typ": "Časový okamžik",

    "datum_a_čas": "2022-10-30T17:00:00"
                    }
                }
            }
        ],

    "typ_události": [
            "https://www.kudyznudy.cz/kalendar-akci/festivaly",
            "https://www.kudyznudy.cz/kalendar-akci/vystavy"
        ],
        "id": "d3ba097e-9ad7-4de2-af3c-18e395dfbbcc",
        "iri": "https://www.kudyznudy.cz/akce/prague-car-festival-2021",
        "název": {
            "cs": "Prague Car Festival 2022"
        },
        "vytvořeno": {
            "typ": "Časový okamžik",

    "datum_a_čas": "2021-09-21T18:57:59.4463373"
        },
        "aktualizováno": {
            "typ": "Časový okamžik",

    "datum_a_čas": "2022-06-03T02:33:10.3900509"
        },
        "popis": {
            "cs": "Největší výstava ve střední a východní Evropě se zaměřením na tuning, motorsport, historické automobily a motocykly."
        },

    "dlouhý_popis": {
            "cs": "Jedenáctý ročník akce evropského významu nabídne vystavovatelům i návštěvníkům \<strong\>jedinečnou podívanou \</strong\>na široké spektrum unikátních automobilů a motocyklů. Výstava spojuje oblasti upravených a sportovních vozů, světa motorsportu a historie vozidel. \<strong\>Prague Car Festival\</strong\> představí na ploše přes 30 000 m\<sup\>2\</sup\> \<strong\>nejlepší tuning z celé Evropy\</strong\>, českou i zahraniční špičku automobilových a motocyklových závodů a průřez bohatou historií vývoje automobilů a motocyklů \<strong\>od předválečného období až po současnost\</strong\>.\r\n\r\n\<h3\>\<br /\>\r\n\<strong\>BSR Tuning Expo\</strong\>\</h3\>\r\nVýstava BSR Tuning Expo nabízí skvosty z české tuningové scény a celé Evropy, profesionální tuningové firmy, sportovní automobily a americké vozy tvoří hlavní obsahovou část výstavních hal. Návštěvníci uvidí přes 500 jedinečných exponátů. K tomu soutěže, workshopy, akční jízdy a srazy na venkovní ploše a také doprovodný program pro celou rodinu vytvářejí jedinečnou příležitost pro návštěvníky všech generací."
        },

    "vhodné_pro_děti": true,

    "vhodné_pro_zvířata": true,

    "za_každého_počasí": true,

    "bezbariériový_přístup": true,

    "časová_náročnost": 2,
        "registrace": {
            "cs": "https://praguecarfestival.cz/vstupenky/"
        },
        "pořadatel": {
            "typ": "Osoba",
            "název": {
                "cs": "Prague Car Festival s.r.o."
            },
            "geometrie": {
                "typ": "Lokalita",
                "souřadnice": [
                    50.12938386099312,
                    14.551111294871998
                ]
            },
            "adresa": {
                "typ": "Kontaktní adresa",
                "ulice": "Hornopočernická 13",
                "obec": "Praha",
                "psč": "19700",
                "kontakt": {
                    "typ": "Kontakt",
                    "email": "mailto:office@praguecarfestival.cz",
                    "mobil": "tel:+420608320336",
                    "facebook": null,
                    "instagram": null,
                    "twitter": null,
                    "url": "https://praguecarfestival.cz/"
                }
            },

    "kontaktní_osoba": {
                "typ": "Kontaktní osoba",
                "jméno": "Martin Apeltauer",
                "email": "mailto:apeltauerm@gmail.com",
                "mobil": "tel:+420608320336"
            }
        },
        "umístění": [
            {
                "typ": "Místo konání",
                "název": {
                    "cs": "PVA EXPO PRAHA Letňany"
                },
                "geometrie": {
                    "typ": "Lokalita",
                    "souřadnice": [
                        50.129423083832535,
                        14.514344635522487
                    ]
                },
                "adresa": {
                    "typ": "Kontaktní adresa",
                    "ulice": "Beranových 667",
                    "obec": "Praha 9",
                    "psč": "19900"
                },

    "kontaktní_osoba": null
            }
        ],

    "kategorie_vstupného": {
            "typ": "Vstupné",
            "cena": {
                "typ": "Částka",
                "výše": 300,
                "měna": "https://publications.europa.eu/resource/authority/currency/CZK"
            },

    "snížená_cena": {
                "typ": "Částka",
                "výše": 200,
                "měna": "https://publications.europa.eu/resource/authority/currency/CZK"
            },

    "rodinné_vstupné": null,
            "poznámka": {
                "cs": "Předprodej on-line za zvýhodněnou cenu."
            }
        },

    "otevírací_doba": null,
        "příloha": [
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/70/70d1a90e-d5c2-4fd1-becb-c531e3ec65be.jpg?v=20220603023310",

    "autor_díla": null,

    "typ_média": "http://www.iana.org/assignments/media-types/image/jpeg",
                "název": {
                    "cs": "PCF"
                },
                "popis": null
            },
            {
                "typ": "Digitální objekt",
                "url": "https://dbcsx3kp2k1lc.cloudfront.net/files/0f/0ff52200-7302-4553-8412-81a307c00d4b.jpg?v=20220603023310",

    "autor_díla": null,

    "typ_média": "http://www.iana.org/assignments/media-types/image/jpeg",
                "název": {
                    "cs": "PCF"
                },
                "popis": null
            }
        ]
    }


| **Hodnota** | **Popis hodnoty** |
| --- | --- |
| **@context** | Popis formátu dat – šablona jsonld (JSON schema: https://www.kudyznudy.cz/kzn/context/v1/udalost.jsonld) |
| **typ** | Událost **(povinné)** |
| **doba_trvání** | Začátek a konec akce **(povinné)** |
| **typ_události** | Pole typů události (první je hlavní) **(povinné)** |
| id | Unikátní GUID záznamu (volitelné) |
| **iri** | Unikání adresa záznamu **(povinné)** |
| **název** | Název článku **(povinné)** |
| **vyvořeno** | Datum a čas vytvoření **(povinné)** |
| **aktualizováno** | Datum a čas poslední změny **(povinné)** |
| **popis** | Krátká anotace (perex) – prostý text **(povinné)** |
| dlouhý_popis | HTML text pokračující za krátkým popisem (volitelné) |
| **vhodné_pro_děti** | Vhodné pro děti (true / false) **(povinné)** |
| **vhodné_pro_zvířata** | Vhodné pro zvířata (true / false) **(povinné)** |
| **za_každého_počasí** | Za každého počasí (true / false) **(povinné)** |
| **bezbariériový_přístup** | Bezbariérový přístup (true / false) **(povinné)** |
| časová_náročnost | Počet hodin potřebných k návštěvě místa (volitelné) |
| registrace | URL adresa registrace na externím webu (volitelné) |
| **pořadatel** | Adresa, a kontakt na pořadatele **(povinné)** |
| umístění | Místa konání (adresy a kontakty) (volitelné) |
| kategorie_vstupného | Ceny jednotlivých typů vstupného (volitelné) |
| otevírací_doba | Definice otevírací doby v jednotlivých dnech (volitelné) |
| **příloha** | Pole obrázků a jejich metadata **(povinné)** |

Každý JSON objekt, který vaše API poskytuje musí být validní oproti danému JSON schématu tak, aby bylo možné ho načíst na straně Kudy z nudy. Pro validaci můžete použít například [JSON Schema Validator](https://www.jsonschemavalidator.net/).
