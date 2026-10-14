## ai_parse_document

```sql
SELECT
    path,
    ai_parse_document(content, MAP('version', '2.0')) AS parsed_content
FROM READ_FILES('/Volumes/my-file', format => 'binaryFile')
```

ai_extract and ai_classify are tuned for the MAP version 2.0

The output includes 'document:elements' (content blocks) and 'document:pages' (page-level breakdown)

## variant_explode

variant_explode => turns a VARIANT array or object into a set of rows with three columns:
pos - position of element
key - field name
value - the field value or array element

```sql
SELECT pos, key, value
FROM variant_explode(parse_json('{"name": "Ada", "age": 36}'));

-- pos | key  | value
-- ----+------+--------
--  0  | name | "Ada"
--  1  | age  | 36
```

LATERAL_VIEW to flatten a semi-structured VARIANT columns into queryable rows

## ai_classify

ai_classify assigns a label to text from a set of categories defined

```sql
WITH elements AS (
  <read_file_logic> t,
  LATERAL variant_explode(parsed:document:elements) AS element
  <additional_WHERE_logic>
),
classified AS (
  SELECT
    content,
    ai_classify(
      content,
      '["property_description","pricing_information","location_details","amenities","host_information","other"]'
    ) AS cls
  FROM elements
)
SELECT
  content,
  cls:response[0] AS category -- Parse the results from ai_classify()
FROM classified
```

the ai_parsed_document produces a VARIANT column called 'parsed_content'. full document instead of a single text string

["invoice",'receipt',"contract","report"] -> categories to choose from

'cls:response[0]' -> the response of the ai_classify( ... ) AS cls

## ai_extract

ai_extract extracts specific fields from unstructured text. You define what to extract and the AI model finds the values

```sql
WITH elements AS (
 <read_file_logic> t,
 <variant_explode_logic>
),
extracted AS (
  SELECT
    content, -- unstructured data/content
    ai_extract(
      content,
      '["invoice_id", "date", "total"]'
    ) AS ext -- the shape of the data is based on what you defined in ai_extract
  FROM elements
)
SELECT
  content,
  ext:response.invoice_id   AS invoice_id,
  ext:response.date    AS date,
  ext:response.total   AS total
FROM extracted
LIMIT 15
```

## ai_prep_search

this is used for boundary detection

```sql
SELECT ai_prep_search(parsed_text) AS chunks
FROM parsed_documents
```

outputs from ai_prep_search:
chunk_to_embed
chunk_to_retrieve
chunk_id
chunk_position