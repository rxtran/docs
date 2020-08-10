# Translations for SIGs

* [Requesting `paraphrases`](#requesting-paraphrases)
* [Query Params](#query-params)
* [Examples](#examples)
  * [Simple Usage](#simple-usage)
  * [Configuring Label URLs](#configuring-label-urls)
* [Unicode Entities](#unicode-entities)

Instead of directly translating SIGs, RxTran uses natural language processing
(NLP) to parse the user-supplied instructions and generate semantically
equivalent phrases we call `paraphrases`. We call the process of generating
paraphrases from sigs `transduction`.

By translating the result of `transduction` instead of the SIG itself, we are
able to expand common short-hand phrases used in prescription instructions and
ensure the source English is translation-friendly.

Because `transduction` introduces multiple, intermediary forms of the SIG, we
require an end-user to choose which `paraphrase` to translate.  This serves as a
confirmation that the generated `paraphrase` is semantically equivalent to the
supplied SIG. For simplicity, we only return the top four (4) `paraphrases`
from highest confidence to lowest confidence the match to the original SIG.

After the end-user selects a paraphrase to translate, the API client can then
request a label as a PNG or GIF image. The target language to translate to is
specified as part of the URL of the request. The label image can be customized
by specifying paper size, font size, and label orientation (landscape or
portrait).

To summarize, generating a translation for SIGs is a 3-step process:

1. Request `paraphrases`
2. Have the end-user choose which `paraphrase` to translate.
3. Request translation of the `paraphrase` into the target language.


## Requesting `paraphrases`

```
GET /paraphrases(:format)?sig&drug_name&drug_ndc_code&label_format&font_size&paper&orientation
```

The `paraphrases` endpoint returns a `transduction` object summarizing the
result of analyzing the SIG. It includes the list of `paraphrases` generated for
the SIG. Each `paraphrase` has a `translation` list for every `locale` the
authenticated account has subscribed to.

Each `translation` object includes the URL to request it as a label. This URL
can be configured using some of the query parameters for this endpoint.

When requesting paraphrases, the drug the instructions are intended for **MUST**
be specified using the `drug_name` and `drug_ndc_code` query params.


### Query Params

| Name            | Required | Description                                         |
| --              | --       | --                                                  |
| `:format`       | No       | Response format. Can be `.xml` or `.json`.          |
| `sig`           | Yes      | The source token to generate `paraphrases` from     |
| `drug_name`     | Yes      | Name of the drug `sig` is intended for              |
| `drug_ndc_code` | Yes      | The NDC code of the drug `sig` is intended for      |
| `label_format`  | No       | Image format to use in the URL of translations      |
| `font_size`     | No       | Font size to use in the URL of translations         |
| `paper`         | No       | Paper size to use in the URL of translations        |
| `orientation`   | No       | Paper orientation to use in the URL of translations |


### Examples

#### Simple Usage

The following requests `paraphrases` with the following parameters:

| Query Param     | Value                      |
| --              | --                         |
| `sig`           | take once a day with water |
| `drug_name`     | apples                     |
| `drug_ndc_code` | 101010101                  |

```
curl -u test_account:passphrase \
  https://api.rxtran.com/paraphrases?sig=take+once+a+day+with+water&drug_name=apples&drug_ndc_code=101010101
```


The response is truncated to show only two `paraphrases` for brevity.

```xml
<?xml version="1.0"?>
<transduction>
  <token>take once a day with water</token>
  <drug>
    <name>apples</name>
    <ndc-code>101010101</ndc-code>
  </drug>
  <paraphrases type="array">
    <paraphrase>
      <id>10cb7265f6d2489adbb36c97d16da353f8770de3</id>
      <token>TAKE WITH WATER. TAKE THIS MEDICINE ONCE A DAY.</token>
      <translations type="array">
        <translation>
          <locale-code>ru_RU</locale-code>
          <token>&#x41F;&#x420;&#x418;&#x41D;&#x418;&#x41C;&#x410;&#x422;&#x42C; &#x421; &#x412;&#x41E;&#x414;&#x41E;&#x419;. &#x41F;&#x420;&#x418;&#x41D;&#x418;&#x41C;&#x410;&#x422;&#x42C; &#x42D;&#x422;&#x41E; &#x41B;&#x415;&#x41A;&#x410;&#x420;&#x421;&#x422;&#x412;&#x41E; &#x41E;&#x414;&#x418;&#x41D; &#x420;&#x410;&#x417; &#x412; &#x414;&#x415;&#x41D;&#x42C;.</token>
          <label-url>https://api.rxtran.com/paraphrases/10cb7265f6d2489adbb36c97d16da353f8770de3.ru_RU.png</label-url>
        </translation>
        <translation>
          <locale-code>es_US</locale-code>
          <token>TOMAR CON AGUA. TOMAR ESTE MEDICAMENTO UNA VEZ AL D&#xCD;A.</token>
          <label-url>https://api.rxtran.com/paraphrases/10cb7265f6d2489adbb36c97d16da353f8770de3.es_US.png</label-url>
        </translation>
      </translations>
    </paraphrase>
    <paraphrase>
      <id>9ab0cef97d0cbcbfea4f878e99bfc57202f5cf16</id>
      <token>TAKE WITH WATER. USE 1 TIMES A DAY.</token>
      <translations type="array">
        <translation>
          <locale-code>ru_RU</locale-code>
          <token>&#x41F;&#x420;&#x418;&#x41D;&#x418;&#x41C;&#x410;&#x422;&#x42C; &#x421; &#x412;&#x41E;&#x414;&#x41E;&#x419;. &#x418;&#x421;&#x41F;&#x41E;&#x41B;&#x42C;&#x417;&#x41E;&#x412;&#x410;&#x422;&#x42C; 1 &#x420;&#x410;&#x417; (&#x410;) &#x412; &#x414;&#x415;&#x41D;&#x42C;.</token>
          <label-url>https://api.rxtran.test/paraphrases/9ab0cef97d0cbcbfea4f878e99bfc57202f5cf16.ru_RU.png</label-url>
        </translation>
        <translation>
          <locale-code>es_US</locale-code>
          <token>TOMAR CON AGUA. USAR 1 VECES AL D&#xCD;A.</token>
          <label-url>https://api.rxtran.test/paraphrases/9ab0cef97d0cbcbfea4f878e99bfc57202f5cf16.es_US.png</label-url>
        </translation>
      </translations>
    </paraphrase>
    ...
  </paraphrases>
</transduction>
```


#### Configuring Label URLs

The `label_format`, `font_size`, `paper`, and `orientation` are passed through
to the URL property of translations.

If requesting the paraphrases

```
curl -u test_account:passphrase \
  https://api.rxtran.com/paraphrases?sig=take+once+a+day&drug_name=oranges&drug_ndc_code=101&font_size=10&paper=RX24
```

The following response, truncated for brevity, shows the image format, font
size, and paper size specified in the URL of the translation.

```xml
<?xml version="1.0"?>
<transduction>
  <token>take once a day</token>
  <drug>
    <name>oranges</name>
    <ndc-code>101</ndc-code>
  </drug>
  <paraphrases type="array">
    <paraphrase>
      <id>c5f59f7219595a3ad9d3f5b44d22ff3d26eb4509</id>
      <token>TAKE THIS MEDICINE ONCE A DAY.</token>
      <translations type="array">
        <translation>
          <locale-code>es_US</locale-code>
          <token>TOMAR ESTE MEDICAMENTO UNA VEZ AL D&#xCD;A.</token>
          <label-url>https://api.rxtran.test/paraphrases/c5f59f7219595a3ad9d3f5b44d22ff3d26eb4509.es_US.gif?font_size=10&paper=RX24</label-url>
        </translation>
        ...
      </translations>
    </paraphrase>
    ...
  </paraphrases>
</transduction>
```


### Unicode Entities

The `<token>` tag in `<translation>` objects may include escaped Unicode
entities. Taking from the [example above](#example-request) above (reproduced
below), the Russian (`ru_RU`) translations are all Unicode entities. The Spanish
(`es_US`) translations include a Unicode entity for the character "í".

The parser used to process the XML or JSON returned by the API will need to
understand how to un-escape these entities into Unicode values.

```xml
<translations type="array">
  <translation>
    <locale-code>ru_RU</locale-code>
    <token>&#x41F;&#x420;&#x418;&#x41D;&#x418;&#x41C;&#x410;&#x422;&#x42C; &#x421; &#x412;&#x41E;&#x414;&#x41E;&#x419;. &#x41F;&#x420;&#x418;&#x41D;&#x418;&#x41C;&#x410;&#x422;&#x42C; &#x42D;&#x422;&#x41E; &#x41B;&#x415;&#x41A;&#x410;&#x420;&#x421;&#x422;&#x412;&#x41E; &#x41E;&#x414;&#x418;&#x41D; &#x420;&#x410;&#x417; &#x412; &#x414;&#x415;&#x41D;&#x42C;.</token>
    <label-url>https://api.rxtran.com/paraphrases/10cb7265f6d2489adbb36c97d16da353f8770de3.ru_RU.png</label-url>
  </translation>
  <translation>
    <locale-code>es_US</locale-code>
    <token>TOMAR CON AGUA. TOMAR ESTE MEDICAMENTO UNA VEZ AL D&#xCD;A.</token>
    <label-url>https://api.rxtran.com/paraphrases/10cb7265f6d2489adbb36c97d16da353f8770de3.es_US.png</label-url>
  </translation>
</translations>
```
